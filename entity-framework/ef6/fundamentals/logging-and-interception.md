---
title: データベース操作のログ記録とインターセプト-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
ms.openlocfilehash: be32ed114269543ac36b256a202e0494d466e4f7
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306529"
---
# <a name="logging-and-intercepting-database-operations"></a>データベース操作のログ記録と受信
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

Entity Framework 6 以降では、Entity Framework は常にコマンドをデータベースに送信します。このコマンドは、アプリケーションコードによって傍受される可能性があります。 これは、SQL をログ記録するために最も一般的に使用されますが、コマンドを変更または中止するために使用することもできます。  

具体的には、EF には次のものが含まれます。  

- DataContext と同様のコンテキストのログプロパティ LINQ to SQL  
- ログに送信される出力の内容と書式をカスタマイズするためのメカニズム  
- インターセプトのための低レベルの構成ブロックにより、制御性と柔軟性が向上  

## <a name="context-log-property"></a>コンテキストログのプロパティ  

DbContext. .Log プロパティは、文字列を受け取る任意のメソッドのデリゲートに設定できます。 最も一般的に使用されるのは、その TextWriter の "書き込み" メソッドに設定することによって、どの TextWriter でも使用されます。 現在のコンテキストによって生成されるすべての SQL は、そのライターにログ記録されます。 たとえば、次のコードでは、SQL がコンソールに記録されます。  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

コンテキストに注意してください。データベース .Log は、Console. 書き込みに設定されています。 これは、SQL をコンソールに記録するために必要なすべてのものです。  

いくつかの出力を確認できるように、単純なクエリ/挿入/更新コードを追加してみましょう。  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

これにより、次の出力が生成されます。  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

(これは、データベースの初期化が既に行われていることを前提とした出力です。 データベースの初期化がまだ行われていない場合は、さらに多くの出力が表示され、新しいデータベースを確認したり、新しいデータベースを作成したりするために、すべてのワーク移行が内部で行われます。  

## <a name="what-gets-logged"></a>ログ記録される内容  

Log プロパティが設定されると、次のすべてがログに記録されます。  

- SQL では、さまざまな種類のコマンドを使用できます。 例えば:  
    - クエリ (通常の LINQ クエリ、eSQL クエリ、SqlQuery などのメソッドからの生のクエリを含む)  
    - SaveChanges の一部として生成された挿入、更新、および削除  
    - 遅延読み込みによって生成されたクエリなどのクエリを読み込むリレーションシップ  
- パラメーター  
- コマンドが非同期的に実行されているかどうか  
- コマンドの実行開始時刻を示すタイムスタンプ  
- コマンドが正常に完了したかどうか、例外をスローして失敗したか、または非同期の場合、が取り消されたかどうか。  
- 結果の値を示す値  
- コマンドの実行に要したおおよその時間です。 これは、コマンドを送信して結果オブジェクトを返すまでの時間です。 結果を読み取る時間は含まれません。  

上記の出力例を見ると、次の4つのコマンドがログに記録されます。  

- コンテキストの呼び出しの結果として得られるクエリ。ブログ。最初  
    - "First" は ToString が呼び出される IQueryable を提供しないため、SQL を取得する ToString メソッドはこのクエリに対して動作しませんでした。  
- ブログの遅延読み込みによって生成されるクエリ。等しく  
    - 遅延読み込みが発生しているキー値のパラメーターの詳細を確認します。  
    - 既定値以外の値に設定されているパラメーターのプロパティのみがログに記録されます。 たとえば、Size プロパティは、0以外の場合にのみ表示されます。  
- SaveChangesAsync によって生成される2つのコマンド1つは、投稿のタイトルを変更する更新用で、もう1つは、新しい投稿を追加するための挿入用です。  
    - FK および Title プロパティのパラメーターの詳細に注意してください。  
    - これらのコマンドは非同期に実行されていることに注意してください。  

## <a name="logging-to-different-places"></a>別の場所へのログ記録  

前述のように、コンソールへのログ記録はとても簡単です。 また、異なる種類の[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)を使用して、メモリやファイルなどに簡単にログを記録することもできます。  

LINQ to SQL に慣れている場合は、EF で log プロパティが実際の TextWriter オブジェクト (たとえば、Console) に設定されているときに、Log プロパティが文字列を受け入れるメソッドに設定されている LINQ to SQL ことに注意してください (たとえば、、Console. write または Console) を入力します。 これは、文字列のシンクとして機能するデリゲートを受け入れることによって、TextWriter から EF を分離するためです。 たとえば、既にいくつかのログ記録フレームワークがあり、次のようなログ記録方法が定義されているとします。  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

これは、次のように EF Log プロパティにフックすることができます。  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>結果のログ記録  

既定の logger では、コマンドがデータベースに送信される前に、コマンドテキスト (SQL)、パラメーター、および "実行中" 行がタイムスタンプと共にログに記録されます。 経過時間を含む "completed" 行は、コマンドの実行後にログに記録されます。  

非同期コマンドの場合、非同期タスクが実際に完了、失敗、またはキャンセルされるまで、"completed" 行は記録されません。  

"Completed" 行には、コマンドの種類と、実行が成功したかどうかによって異なる情報が含まれています。  

### <a name="successful-execution"></a>成功した実行  

正常に完了したコマンドの場合、出力は "x ミリ秒で完了しました。結果:" の後に結果を示すいくつかの結果が続きます。 データリーダーを返すコマンドの場合は、返される[Dbdatareader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx)の型が結果に示されます。 上に示した update コマンドなどの整数値を返すコマンドは、その整数です。  

### <a name="failed-execution"></a>失敗した実行  

例外をスローして失敗するコマンドの場合、出力には例外からのメッセージが含まれます。 たとえば、SqlQuery を使用して、存在するテーブルに対してクエリを実行すると、次のようなログ出力が生成されます。  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>実行の取り消し  

タスクが取り消された非同期コマンドの場合、結果は例外で失敗する可能性があります。これは、基になる ADO.NET プロバイダーが取り消しを試行したときに頻繁に実行されるためです。 これが行われず、タスクが正常に取り消されると、出力は次のようになります。  

```  
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>ログの内容と書式設定の変更  

内部では、データベース .Log プロパティは DatabaseLogFormatter オブジェクトを使用します。 このオブジェクトは、IDbCommandInterceptor の実装 (下記参照) を文字列および DbContext を受け入れるデリゲートに効果的にバインドします。 これは、EF によってコマンドが実行される前と後に、DatabaseLogFormatter のメソッドが呼び出されることを意味します。 これらの DatabaseLogFormatter メソッドは、ログ出力を収集して書式設定し、デリゲートに送信します。  

### <a name="customizing-databaselogformatter"></a>DatabaseLogFormatter のカスタマイズ  

ログ記録とその書式設定を変更するには、DatabaseLogFormatter から派生する新しいクラスを作成し、必要に応じてメソッドをオーバーライドします。 オーバーライドする最も一般的な方法は次のとおりです。  

- LogCommand –実行前にコマンドがログに記録される方法を変更するには、これをオーバーライドします。 既定では、LogCommand は各パラメーターの Logcommand を呼び出します。オーバーライドで同じ操作を行うことも、パラメーターを別の方法で処理することもできます。  
- LogResult –これをオーバーライドして、コマンドの実行結果がどのようにログに記録されるかを変更します。  
- LogParameter –パラメーターログの書式設定と内容を変更するには、これをオーバーライドします。  

たとえば、各コマンドがデータベースに送信される前に1行だけログを記録したいとします。 これは、次の2つの上書きを使用して行うことができます。  

- SQL の単一行をフォーマットして書き込むには、LogCommand をオーバーライドします。  
- LogResult をオーバーライドして何も実行しないようにします。  

コードは次のようになります。

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

出力をログに記録するには、書き込みメソッドを呼び出して、構成された書き込みデリゲートに出力を送信します。  

(このコードでは、例と同じように改行を単純に削除します。 多くの場合、複雑な SQL の表示には適していません)。  

### <a name="setting-the-databaselogformatter"></a>DatabaseLogFormatter の設定  

新しい DatabaseLogFormatter クラスを作成したら、EF に登録する必要があります。 これは、コードベースの構成を使用して行います。 簡単に言うと、これは、Dbconfiguration クラスと同じアセンブリ内の DbConfiguration から派生した新しいクラスを作成し、この新しいクラスのコンストラクターで SetDatabaseLogFormatter を呼び出すことを意味します。 例えば:  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>新しい DatabaseLogFormatter の使用  

この新しい DatabaseLogFormatter は、いつでもデータベース .Log が設定されるようになりました。 そのため、パート1のコードを実行すると、次の出力が生成されるようになります。  

```  
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>インターセプトの構成要素  

ここまでは、EF によって生成された SQL をログに記録するために、DbContext を使用する方法を見てきました。 しかし、このコードは実際には、より一般的なインターセプトを行うために、一部の低レベルの構成要素に対して比較的シンファサードです。  

### <a name="interception-interfaces"></a>インターセプトインターフェイス  

インターセプトコードは、インターセプトインターフェイスの概念を中心に構築されています。 これらのインターフェイスは、IDbInterceptor から継承し、EF が何らかのアクションを実行するときに呼び出されるメソッドを定義します。 目的は、オブジェクトの型ごとに1つのインターフェイスを受け取ることです。 たとえば、IDbCommandInterceptor インターフェイスは、EF が ExecuteNonQuery、ExecuteScalar、ExecuteReader、および関連メソッドの呼び出しを行う前に呼び出されるメソッドを定義します。 同様に、インターフェイスは、これらの各操作が完了したときに呼び出されるメソッドを定義します。 上で見た DatabaseLogFormatter クラスは、このインターフェイスを実装してコマンドを記録します。  

### <a name="the-interception-context"></a>インターセプトコンテキスト  

任意のインターセプターインターフェイスで定義されているメソッドを見ると、すべての呼び出しには DbInterceptionContext 型のオブジェクト、または DbCommandInterceptionContext\<\>などの派生型のオブジェクトが指定されていることがわかります。 このオブジェクトには、EF が行っているアクションに関するコンテキスト情報が含まれています。 たとえば、アクションが DbContext の代わりに実行されている場合、DbContext は DbInterceptionContext に含まれます。 同様に、非同期に実行されるコマンドの場合、IsAsync フラグは DbCommandInterceptionContext に設定されます。  

### <a name="result-handling"></a>結果の処理  

\< DbCommandInterceptionContext\>クラスには、Result、originalresult、Exception、および originalresult というプロパティが含まれています。 これらのプロパティは、操作が実行される前に呼び出されるインターセプトメソッド (つまり...) の呼び出しに対して null または0に設定されます。メソッドを実行しています。 操作が実行され、成功した場合、Result と OriginalResult は操作の結果に設定されます。 これらの値は、操作が実行された後に呼び出されるインターセプトメソッド (つまり...実行されたメソッド。 同様に、操作がをスローした場合、Exception プロパティと OriginalException プロパティが設定されます。  

#### <a name="suppressing-execution"></a>実行の抑制  

コマンドが実行される前にインターセプターが Result プロパティを設定する場合 (メソッドを実行すると、EF は実際にはコマンドを実行しませんが、代わりに結果セットを使用します。 つまり、インターセプターはコマンドの実行を抑制できますが、コマンドが実行されたかのように EF を続行します。  

これがどのように使用されるかの例として、通常、ラッピングプロバイダーで実行されたコマンドバッチ処理があります。 インターセプターは、後で実行するためにコマンドをバッチとして格納しますが、コマンドが通常どおり実行されたことを EF に "示す" とします。 バッチ処理を実装するには、これを超えるものが必要であることに注意してください。ただし、これはインターセプトの結果を変更する方法の例です。  

[例外] プロパティを設定することにより、実行を抑制することもできます。メソッドを実行しています。 これにより、指定された例外をスローすることで、操作の実行が失敗した場合と同様に EF が続行します。 もちろん、これによってアプリケーションがクラッシュすることがありますが、一時的な例外や EF によって処理される他の例外である場合もあります。 たとえば、これをテスト環境で使用して、コマンドの実行が失敗したときにアプリケーションの動作をテストすることができます。  

#### <a name="changing-the-result-after-execution"></a>実行後の結果の変更  

コマンドが実行された後にインターセプターが Result プロパティを設定する場合 (実行されたメソッド)、EF は、実際に操作から返された結果ではなく、変更された結果を使用します。 同様に、コマンドの実行後にインターセプターが Exception プロパティを設定した場合、EF は、操作によって例外がスローされたかのように set 例外をスローします。  

インターセプターでは、例外をスローする必要がないことを示すために、Exception プロパティを null に設定することもできます。 これは、操作の実行に失敗しても、操作が成功したかのように、インターセプターが EF を使用する場合に便利です。 これには、通常、EF が処理を続行するための結果値があるように結果を設定する必要もあります。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult と Originalresult  

EF によって操作が実行されると、実行が失敗した場合は Result プロパティと OriginalResult プロパティが設定され、例外が発生して実行が失敗した場合は例外および Originalresult プロパティが設定されます。  

OriginalResult プロパティと Originalresult プロパティは読み取り専用であり、実際に操作を実行した後に EF によってのみ設定されます。 これらのプロパティは、インターセプターでは設定できません。 つまり、どのインターセプターも、他のインターセプターによって設定された例外と結果を、実際の例外や、操作の実行時に発生した結果と区別することはできません。  

### <a name="registering-interceptors"></a>インターセプターの登録  

1つ以上のインターセプトインターフェイスを実装するクラスが作成されたら、DbInterception クラスを使用して EF に登録できます。 例えば:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

インターセプターは、DbConfiguration コードベースの構成メカニズムを使用して、アプリドメインレベルで登録することもできます。  

### <a name="example-logging-to-nlog"></a>例:NLog へのログ記録  

ここでは、IDbCommandInterceptor と[Nlog](http://nlog-project.org/)を使用して、次のことを行う例について説明します。  

- 非同期的に実行されていないコマンドに対して警告をログに記録します。  
- 実行時にスローされるすべてのコマンドに対してエラーをログに記録します。  

ログ記録を実行するクラスを次に示します。これは、上記のように登録する必要があります。  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

このコードでは、コマンドが非同期に実行されていないことを検出し、コマンドの実行中にエラーが発生したことを検出するために、インターセプトコンテキストを使用する方法に注意してください。  
