---
title: ログ記録と EF6 のデータベース操作の受信
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
ms.openlocfilehash: 3f06e073f3ab6e46883663620219e302d5db1d60
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490088"
---
# <a name="logging-and-intercepting-database-operations"></a>ログに記録して、データベース操作の受信
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

ときにいつでも Entity Framework コマンドを送信、データベースにこのコマンドは、Entity Framework 6 では、以降は、アプリケーション コードで受け取ることができます。 これは、SQL では、ログ記録が最もよく使用されますが、変更、または、コマンドを中止するも使用できます。  

具体的には、EF が含まれます。  

- LINQ to SQL で DataContext.Log のようなコンテキストのログ プロパティ  
- コンテンツと、ログに送信される出力の書式設定をカスタマイズするためのメカニズム  
- コントロールと柔軟性を提供する傍受のための低レベルの構成要素  

## <a name="context-log-property"></a>コンテキストのログ プロパティ  

DbContext.Database.Log プロパティは、文字列を受け取る任意のメソッドのデリゲートを設定できます。 最もよくその TextWriter の「書き込み」メソッドに設定することで、TextWriter に使用されます。 現在のコンテキストによって生成されたすべての SQL は、そのライターに記録されます。 たとえば、次のコードは SQL をコンソールにログインします。  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

そのコンテキストに注意してください。Console.Write Database.Log が設定されます。 これは、すべての SQL をコンソールにログインするために必要です。  

何らかの出力を確認できるようにしましょうクエリ/挿入/更新する単純なコードを追加します。  

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

(これは、データベースの初期化が既に過ぎていると仮定すると出力であることに注意してください。 データベースの初期化が既に行われていない場合がありますし、移行のすべての作業を表示、はるかに多くの出力が内部でのチェックまたは新しいデータベースを作成します。)  

## <a name="what-gets-logged"></a>ログに記録内容を取得しますか。  

ときにログ プロパティの設定は、次のすべてのログに記録されます。  

- コマンドのさまざまな種類のすべての SQL です。 例えば:  
    - 通常の LINQ クエリ、eSQL クエリ、および SqlQuery などのメソッドからの生のクエリを含むクエリ  
    - 挿入、更新、および SaveChanges の一部として生成された削除  
    - リレーションシップの遅延読み込みによって生成されるものなどのクエリの読み込み  
- パラメーター  
- コマンドが非同期的に実行されているかどうか  
- コマンドが実行を開始したことを示すタイムスタンプ  
- 例外をスローするか、非同期コマンドは正常に完了しましたが失敗しました、かどうかが取り消されました  
- 結果の値のいくつかを示す値  
- コマンドを実行するのに要した概算の時間。 結果オブジェクトを取得するコマンドの送信からの時間であるに注意してください。 結果の読み取りに時間は含まれません。  

上記の例の出力を見ると、各ログに記録する 4 つのコマンドは。  

- クエリのコンテキストへの呼び出しの結果します。Blogs.First  
    - メソッドは、SQL を取得するためには、このクエリの動作しません ToString"First"が提供しないこと、IQueryable を ToString を呼び出すことができますに注意してください。  
- ブログの遅延読み込みをクエリします。投稿  
    - どの遅延読み込みのキー値のパラメーターの詳細が行われることに注意してください。  
    - 既定以外の値に設定されているパラメーターのプロパティのみが記録されます。 たとえば、サイズ プロパティのみのように 0 でないかどうかは。  
- SaveChangesAsync; から結果として 2 つのコマンド1 つは、更新、挿入する新しい投稿を追加する他の投稿のタイトルを変更するには  
    - FK とタイトルのプロパティのパラメーターの詳細に注意してください。  
    - これらのコマンドを非同期的に実行されていることに注意してください。  

## <a name="logging-to-different-places"></a>別の場所にログ記録  

上記のログ記録をコンソールはとても簡単です。 簡単にメモリやファイルなどさまざまな種類を使用してログインの[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)します。  

LINQ to SQL で LINQ to SQL のログ プロパティが TextWriter で実際のオブジェクト (たとえば、Console.Out) 中にログ設定されます (たとえば、文字列を受け取るメソッドに EF に注意が使い慣れている場合、Console.Write または Console.Out.Write)。 この理由は、文字列をシンクとして機能するすべてのデリゲートをそのまま使用して EF TextWriter からを分離するためです。 たとえば、いくつかのログ記録フレームワークが既にあることと、ログ記録メソッドを定義します次のようにします。  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

このような EF のログ プロパティにこれフックする可能性があります。  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>結果のログ記録  

既定のロガー ログ コマンド テキスト (SQL)、パラメーター、および「実行」の行のタイムスタンプを持つデータベースにコマンドが送信される前にします。 経過時間を含む「完了」の行では、コマンドのログに記録された次の実行です。  

非同期タスク実際に完了、失敗した場合、またはキャンセルされるまでは、非同期の「完了」の行をコマンドには記録されません。  

「完了」の行には、コマンドと実行が成功したかどうかの種類に応じて、異なる情報が含まれています。  

### <a name="successful-execution"></a>正常に実行  

出力を正常に完了するためのコマンドは"結果を使用した ms x で完了しました:"後に、結果の内容の一部を示す値。 データ リーダーの結果を返すコマンドを示す値は、型[DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx)が返されます。 更新プログラムなどの整数値を返すコマンドに示す結果上に示したコマンドは整数になります。  

### <a name="failed-execution"></a>失敗した実行  

例外をスローして失敗したコマンドの出力には、例外からのメッセージが含まれています。 たとえば、SqlQuery が存在するテーブルに対するクエリを使用してログで結果の出力はこのようなもの。  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>取り消された実行  

非同期コマンドが、タスクはキャンセル結果でした失敗、例外のためにこれは、基になる ADO.NET プロバイダー多くの場合、キャンセルを試行したときに。 そうしない場合、出力は次のようになりますし、タスクは正常にキャンセルします。  

```  
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>ログの内容を変更して、書式設定  

実際には DatabaseLogFormatter オブジェクトのプロパティは、Database.Log を使用します。 このオブジェクトは、文字列と DbContext をデリゲートに IDbCommandInterceptor 実装 (下記参照) を効果的にバインドします。 これは、ある DatabaseLogFormatter メソッドによって呼び出されるコマンドの実行の前後に EF を意味します。 これらの DatabaseLogFormatter メソッドは、収集し、ログ出力を書式設定し、代理人に送信します。  

### <a name="customizing-databaselogformatter"></a>DatabaseLogFormatter をカスタマイズします。  

DatabaseLogFormatter から派生し、適切なメソッドを上書きする新しいクラスを作成してログ内容と書式設定方法の変更を実現できます。 オーバーライドする最も一般的な方法を示します。  

- LogCommand – これをオーバーライドして、それらが実行される前にコマンドを記録する方法を変更します。 既定で LogCommand は各パラメーターの LogParameter を呼び出すオーバーライドで同じ処理を実行または代わりに異なる方法でパラメーターを処理することができます。  
- LogResult – これをオーバーライドしてから、コマンドの実行結果を記録する方法を変更します。  
- LogParameter – これをオーバーライドして、書式設定とパラメーターのログ記録の内容を変更します。  

たとえば、各コマンドは、データベースに送信される前に、1 行だけを記録したいとします。 これは、2 つの上書きを実行できます。  

- 書式設定および SQL の 1 つの行を記述する LogCommand をオーバーライドします。  
- 何もしない LogResult をオーバーライドします。  

コードは、次のようになります。

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

ログインをかけるだけで構成された書き込みのデリゲートに出力を送信 Write メソッドを出力します。  

(このコードで改行の例と同様の単純な削除はことに注意してください。 可能性があります機能複雑な SQL を表示します。)  

### <a name="setting-the-databaselogformatter"></a>設定、DatabaseLogFormatter  

その新しい DatabaseLogFormatter クラスを作成した後は、EF に登録する必要があります。 これは、コード ベースの構成を使用します。 一言では、DbContext クラスと同じアセンブリ内の DbConfiguration から派生した新しいクラスを作成して、この新しいクラスのコンストラクターで呼び出して SetDatabaseLogFormatter を意味します。 例えば:  

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

### <a name="using-the-new-databaselogformatter"></a>新しい DatabaseLogFormatter を使用します。  

この新しい DatabaseLogFormatter Database.Log が設定されているときにいつでも使用されます。 そのため、第 1 部からコードを実行しているが、次の出力では発生ようになりました。  

```  
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>インターセプション構成要素  

これまでに DbContext.Database.Log を使用して、EF によって生成される SQL ログに記録する方法が説明しました。 このコードは、一般的な傍受のための低レベルの構成要素を実際には比較的薄いファサードです。  

### <a name="interception-interfaces"></a>インターセプション インターフェイス  

インターセプション コード インターセプション インターフェイスの概念が構築されています。 これらのインターフェイスは IDbInterceptor から継承し、EF が何らかのアクションを実行するときに呼び出されるメソッドを定義します。 目的は、1 つのインターフェイスが傍受されないオブジェクトの種類ごとにです。 たとえば、IDbCommandInterceptor インターフェイスは、EF は ExecuteNonQuery、ExecuteScalar、ExecuteReader、および関連するメソッドの呼び出し前に呼び出されるメソッドを定義します。 同様に、インターフェイスは、これらの各操作が完了したときに呼び出されるメソッドを定義します。 これまで見てきた上を DatabaseLogFormatter クラスは、コマンドをログには、このインターフェイスを実装します。  

### <a name="the-interception-context"></a>インターセプトのコンテキスト  

インターセプター インターフェイスのいずれかで定義されているメソッドで検索することことすべての呼び出しは指定された型 DbInterceptionContext のオブジェクトまたは何らかの種類から派生したこの DbCommandInterceptionContext など明らかな\<\>します。 このオブジェクトには、EF がかかっている操作に関するコンテキスト情報が含まれています。 たとえば、DbContext に代わって、この操作を実行中は場合、DbContext は、DbInterceptionContext で含まし。 同様に、非同期的に実行されているコマンドについては、DbCommandInterceptionContext にへの IsAsync フラグが設定します。  

### <a name="result-handling"></a>結果の処理  

DbCommandInterceptionContext\< \>クラスには、結果、OriginalResult、例外、および OriginalException と呼ばれるプロパティが含まれています。 これらのプロパティ設定に null/0、操作を実行する前に呼び出されるインターセプターのメソッドを呼び出し、つまりの.メソッドを実行します。 操作が実行され、成功すると場合、結果と OriginalResult し、設定操作の結果にするされます。 操作が実行された後に呼び出されるインターセプターのメソッドで、これらの値を確認しできます-がの.メソッドを実行します。 同様に、操作をスローする場合、例外と OriginalException プロパティは設定されます。  

#### <a name="suppressing-execution"></a>実行を抑制します。  

コマンドが実行される前に、インターセプターが、Result プロパティを設定かどうか (のいずれかで、...メソッドの実行) し、EF は、コマンドを実際に実行を試行しませんが、代わりにだけ、結果セット。 つまり、インターセプターは、コマンドの実行を抑制するが、続行コマンドが実行された場合、EF があります。  

これを使用する方法の例では、ラッピング プロバイダーでコマンドをバッチ処理が実現していました。 インターセプターは後で実行するためのコマンドをバッチとして保存しますを通常どおり、コマンドを実行した EF「ふり」は。 、バッチ処理を実装するためにそれ以上が必要ですが、これは、傍受される結果の変更を使用する方法の例に注意してください。  

いずれかで例外のプロパティを設定して実行を抑制することも、.メソッドを実行します。 これにより、EF に指定した例外をスローして、操作の実行に失敗したかのように続行されます。 これが、もちろん、原因でクラッシュし、アプリケーションが一時的な例外や EF によって処理されるその他のいくつかの例外があります。 たとえば、このされる可能性がありますテスト環境でコマンドの実行が失敗したときに、アプリケーションの動作をテストします。  

#### <a name="changing-the-result-after-execution"></a>実行後、結果を変更します。  

コマンドが実行された後にインターセプターがかどうかに結果のプロパティを設定 (のいずれかで、...メソッドの実行)、EF は実際には、操作から返された結果ではなく、変更された結果を使用します。 同様に、インターセプターは、コマンドが実行された後に、例外のプロパティを設定、されている場合、EF セット例外がスローされます、操作は、例外をスローしていた場合。  

インターセプターは、例外をスローしないことを示す null に例外プロパティを設定することができますも。 これは、操作の実行に失敗しましたが、インターセプターが EF 操作が正常に完了した場合、続行する場合に役立ちます。 これは通常必要もあります EF がある継続を使用するいくつかの結果値を持つように、結果を設定します。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult と OriginalException  

EF の操作が実行された後に設定されます実行は失敗しなかった場合の結果と OriginalResult プロパティか、例外および OriginalException プロパティ実行例外で失敗しました。  

OriginalResult と OriginalException プロパティは読み取り専用と、実際に操作を実行した後、EF によってのみ設定されます。 インターセプターでは、これらのプロパティを設定できません。 つまり、インターセプターは、例外または結果では実際の例外ではなく他のいくつかのインターセプタによって設定されているか、操作を実行したときに発生した結果の間で区別できます。  

### <a name="registering-interceptors"></a>インターセプターを登録します。  

インターセプトのインターフェイスを実装するクラスが作成されたら、DbInterception クラスを使用して EF で登録できます。 例えば:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

インターセプターは、DbConfiguration コード ベースの構成機構を使用して、アプリケーション ドメイン レベルで登録することもできます。  

### <a name="example-logging-to-nlog"></a>例: ログ NLog を  

まとめてみましょうすべてこれには、例を使用して IDbCommandInterceptor と[NLog](http://nlog-project.org/)に。  

- ログの警告を非非同期的に実行される任意のコマンド  
- 実行されたときにスローする任意のコマンドのエラー ログに記録します。  

上記のように登録する必要がありますが、ログ記録を行うクラスを次に示します。  

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

このコードが傍受されるコンテキストを使用するコマンドを非非同期的に実行されているときに検出して、コマンドを実行中にエラーがある場合を検出する方法に注意してください。  
