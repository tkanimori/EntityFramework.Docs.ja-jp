---
title: インターセプター-EF Core
description: データベース操作とその他のイベントのインターセプト
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 61ec6968344798af8ecffb878a1e47a6a8e031cd
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503203"
---
# <a name="interceptors"></a>インターセプター

Entity Framework Core (EF Core) インターセプターを使用すると、EF Core 操作のインターセプト、変更、または抑制を有効にできます。 これには、コマンドの実行などの低レベルのデータベース操作や、SaveChanges の呼び出しなどの上位レベルの操作が含まれます。

インターセプターは、傍受される操作の変更または抑制を許可するという点で、ログ記録と診断とは異なります。 ログ記録の[選択肢とし](xref:core/logging-events-diagnostics/extensions-logging)ては、[単純なログ](xref:core/logging-events-diagnostics/simple-logging)記録や、ログ記録の方が適しています。

インターセプターは、コンテキストが構成されているときに DbContext インスタンスごとに登録されます。 [診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用して、プロセス内のすべての dbcontext インスタンスで同じ情報を取得します。

## <a name="registering-interceptors"></a>インターセプターの登録

インターセプターは <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [、dbcontext インスタンスを構成](xref:core/dbcontext-configuration/index)するときにを使用して登録されます。 これは、通常、のオーバーライドで行われ <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> ます。 次に例を示します。

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

`AddInterceptors`または、の一部として、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> または <xref:Microsoft.EntityFrameworkCore.DbContextOptions> dbcontext コンストラクターに渡すインスタンスを作成するときに、を呼び出すこともできます。

> [!TIP]
> AddDbContext が使用されている場合、または DbContext コンストラクターに DbContextOptions インスタンスが渡された場合、OnConfiguring は引き続き呼び出されます。 これにより、DbContext の構築方法に関係なく、コンテキスト構成を適用するための理想的な場所となります。

通常、インターセプターはステートレスであるため、1つのインターセプターインスタンスをすべての DbContext インスタンスに使用できます。 次に例を示します。

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor 
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

すべてのインターセプターインスタンスは、から派生した1つ以上のインターフェイスを実装する必要があり <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> ます。 各インスタンスは、複数のインターセプトインターフェイスを実装している場合でも、1回だけ登録する必要があります。EF Core は、必要に応じて各インターフェイスのイベントをルーティングします。

## <a name="database-interception"></a>データベースの傍受

> [!NOTE]
> データベースインターセプトは EF Core 3.0 で追加されたため、リレーショナルデータベースプロバイダーでのみ使用できます。
> EF Core 5.0 でセーブポイントのサポートが追加されました。

低レベルのデータベース傍受は、次の表に示す3つのインターフェイスに分割されます。

| Bam                                                            | インターセプトしたデータベース操作
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | コマンドの作成</br>コマンドの実行</br>コマンドの失敗</br>コマンドの DbDataReader を破棄します。
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | 接続の開始と終了</br>接続エラー
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | トランザクションの作成</br>既存のトランザクションの使用</br>トランザクションのコミット</br>トランザクションのロールバック</br>セーブポイントの作成と使用</br>トランザクションエラー

基底クラス、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> 、およびには、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> 対応するインターフェイスの各メソッドに対する非 op 実装が含まれています。 基底クラスを使用して、不要なインターセプトメソッドを実装する必要がないようにします。

各インターセプター型のメソッドはペアになり、データベース操作が開始される前に最初に呼び出され、操作が完了した後に2番目のが呼び出されます。 次に例を示します。 たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> クエリが実行される前にが呼び出され、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> クエリがデータベースに送信された後にが呼び出されます。

メソッドの各ペアには、同期と非同期の両方のバリエーションがあります。 これにより、アクセストークンの要求などの非同期 i/o を、非同期データベース操作のインターセプトの一部として実行できます。

### <a name="example-command-interception-to-add-query-hints"></a>例: クエリヒントを追加するためのコマンドインターセプト

> [!TIP]  
> [コマンドインターセプターのサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception)GitHub からダウンロードできます。

は、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> データベースに送信される前に SQL を変更するために使用できます。 この例では、クエリヒントを含めるように SQL を変更する方法を示します。

多くの場合、インターセプトの難しい部分は、コマンドが変更する必要があるクエリに対応するかどうかを判断することです。 SQL の解析は1つのオプションですが、脆弱になる傾向があります。 別の方法として、 [EF Core クエリタグ](xref:core/querying/tags) を使用して、変更する各クエリにタグを付けることもできます。 次に例を示します。

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

このタグは、コマンドテキストの最初の行にコメントとして常に含まれるため、インターセプターで検出できます。 タグを検出すると、クエリ SQL が変更され、適切なヒントが追加されます。

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

注意:

* インターセプターは、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> インターセプターインターフェイスにすべてのメソッドを実装する必要がないように、から継承します。
* インターセプターは、同期メソッドと非同期メソッドの両方を実装します。 これにより、同期クエリと非同期クエリに同じクエリヒントが確実に適用されます。
* インターセプターは、生成された `Executing` SQL と共に EF Core によって呼び出されるメソッドを実装し _てから、_ データベースに送信されます。 これ `Executed` は、データベースの呼び出しが返された後に呼び出されるメソッドと比較します。

この例のコードを実行すると、クエリにタグが付けられると、次のように生成されます。

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

一方、クエリにタグが付けられていない場合、そのクエリは変更されずにデータベースに送信されます。

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>例: ADD を使用した SQL Azure 認証の接続の傍受

> [!TIP]  
> [接続インターセプターのサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception)GitHub からダウンロードできます。

は <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> 、データベースへの接続に使用する前に、を操作するために使用でき <xref:System.Data.Common.DbConnection> ます。 これは、Azure Active Directory (AAD) アクセストークンを取得するために使用できます。 次に例を示します。

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> [Microsoft](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) では、接続文字列を使用して AAD 認証をサポートするようになりました。 詳細については、「 <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> 」を参照してください。

> [!WARNING]
> 接続を開く同期呼び出しが行われた場合、インターセプターはをスローすることに注意してください。 これは、アクセストークンを取得するための非同期メソッドがないためです。非同期の [コンテキストから非同期のメソッドを呼び出す方法は、デッドロックのリスクを伴うことなく、汎用的な](https://devblogs.microsoft.com/dotnet/configureawait-faq/)方法ではありません。

> [!WARNING]
> 場合によっては、アクセストークンが Azure トークンプロバイダーに自動的にキャッシュされないことがあります。 要求されたトークンの種類によっては、ここで独自のキャッシュを実装することが必要になる場合があります。

### <a name="example-advanced-command-interception-for-caching"></a>例: キャッシュ用の高度なコマンドインターセプト

> [!TIP]  
> [詳細なコマンドインターセプターサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception)GitHub からダウンロードできます。

EF Core インターセプターは次のことができます。

* インターセプト中の操作の実行を抑制するように EF Core に指示します
* に報告された操作の結果を変更 EF Core

この例は、これらの機能を使用して、プリミティブな第2レベルのキャッシュのように動作するインターセプターを示しています。 キャッシュされたクエリ結果が特定のクエリに対して返され、データベースのラウンドトリップを回避できます。

> [!WARNING]
> この方法で EF Core の既定の動作を変更する場合は注意が必要です。 EF Core は、正常に処理できない異常な結果が返された場合に予期しない方法で動作することがあります。 また、この例ではインターセプターの概念を示しています。これは、堅牢な第2レベルのキャッシュ実装のテンプレートとしては意図されていません。

この例では、アプリケーションは多くの場合、最新の "毎日のメッセージ" を取得するクエリを実行します。

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

このクエリは、インターセプターで簡単に検出できるように [タグ付け](xref:core/querying/tags) されています。 これは、データベースに対して、毎日1回だけ新しいメッセージを照会することです。 それ以外の場合、アプリケーションはキャッシュされた結果を使用します。 (サンプルでは、新しい日をシミュレートするために、サンプルで10秒の遅延を使用しています)。

#### <a name="interceptor-state"></a>インターセプターの状態

このインターセプターはステートフルなので、クエリが実行された最新の毎日のメッセージの ID とメッセージテキストが格納されます。 この状態により、キャッシュでは同じインターセプターを複数のコンテキストインスタンスで使用する必要があるため、 [ロック](/dotnet/csharp/language-reference/keywords/lock-statement) も必要になります。

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>実行前

メソッドで `Executing` (つまり、データベース呼び出しを行う前)、インターセプターはタグ付きクエリを検出し、キャッシュされた結果があるかどうかを確認します。 そのような結果が見つかった場合は、クエリが抑制され、キャッシュされた結果が代わりに使用されます。

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

コードによってが呼び出され、キャッシュされ <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> たデータを含む置換が渡されることに注意して <xref:System.Data.Common.DbDataReader> ください。 この InterceptionResult が返され、クエリの実行が抑制されます。 代わりに、クエリの結果として EF Core によって置換リーダーが使用されます。

このインターセプターは、コマンドテキストも操作します。 この操作は必要ありませんが、ログメッセージのわかりやすさが向上しています。 クエリが実行されないため、コマンドテキストが有効な SQL である必要はありません。

#### <a name="after-execution"></a>実行後

キャッシュされたメッセージがない場合、または有効期限が切れている場合、上記のコードでは結果が抑制されません。 そのため、EF Core は通常どおりにクエリを実行します。 その後、実行後にインターセプターの `Executed` メソッドに戻ります。 この時点で、結果がまだキャッシュされたリーダーでない場合、新しいメッセージ ID と文字列は実際のリーダーから exacted され、このクエリの次回の使用に備えてキャッシュされます。

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a>デモンストレーション

[キャッシュインターセプターサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception)には、キャッシュをテストするための毎日のメッセージを照会する単純なコンソールアプリケーションが含まれています。

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }
        
        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

次の出力が生成されます。

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

ログ出力から、タイムアウトが経過するまでアプリケーションがキャッシュされたメッセージを使用し続けていることがわかります。この時点で、新しいメッセージに対してデータベースに対してクエリが再度実行されます。

## <a name="savechanges-interception"></a>SaveChanges インターセプト

> [!NOTE]
> SaveChanges インターセプトが EF Core 5.0 で追加されました。

> [!TIP]  
> [SaveChanges インターセプターサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)GitHub からダウンロードできます。

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> および <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> インターセプトポイントは、 `ISaveChangesInterceptor` <!-- Issue #2748 --> efi. 他のインターセプターと同様、 `SaveChangesInterceptor` <!-- Issue #2748 --> 非 op メソッドを含む基本クラスは、便宜上提供されています。

> [!TIP]
> インターセプターは強力です。 ただし、多くの場合、SaveChanges メソッドをオーバーライドするか、DbContext で公開されている [savechanges に .net イベント](xref:core/logging-events-diagnostics/events) を使用する方が簡単な場合があります。

### <a name="example-savechanges-interception-for-auditing"></a>例: 監査のための SaveChanges インターセプト

SaveChanges をインターセプトして、加えられた変更の独立した監査レコードを作成できます。

> [!NOTE]
> これは、堅牢な監査ソリューションとしては想定されていません。 むしろ、インターセプトの機能を示すために使用される単純な例です。

#### <a name="the-application-context"></a>アプリケーションコンテキスト

[監査のサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)では、ブログと投稿を含む単純な dbcontext を使用します。

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

各 DbContext インスタンスに対して、インターセプターの新しいインスタンスが登録されていることに注意してください。 これは、監査インターセプターには、現在のコンテキストインスタンスにリンクされている状態が含まれているためです。

#### <a name="the-audit-context"></a>監査コンテキスト

このサンプルには、監査データベースに使用される2つ目の DbContext と model も含まれています。

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a>インターセプター

インターセプターを使用した監査に関する一般的な考え方は次のとおりです。

* SaveChanges の先頭に監査メッセージが作成され、監査データベースに書き込まれます。
* SaveChanges を続行できます
* SaveChanges が成功すると、監査メッセージが更新され、成功を示すメッセージが表示されます。
* SaveChanges が失敗した場合は、失敗を示す監査メッセージが更新されます。

最初の段階は、の上書きを使用してデータベースに変更が送信される前に処理されます。 `ISaveChangesInterceptor.SavingChanges` <!-- Issue #2748 --> そして `ISaveChangesInterceptor.SavingChangesAsync`<!-- Issue #2748 -->.

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

同期と非同期の両方のメソッドをオーバーライドすると、SaveChanges または SaveChangesAsync が呼び出されているかどうかにかかわらず、監査が確実に行われます。 非同期のオーバーロードは、監査データベースに対して非ブロッキング非同期 i/o を実行できることにも注意してください。 すべてのデータベース i/o が非同期であることを確認するために、sync SavingChanges メソッドからをスローすることをお勧めします。 この場合、アプリケーションは常に SaveChangesAsync を呼び出し、SaveChanges を呼び出す必要はありません。

#### <a name="the-audit-message"></a>監査メッセージ

すべてのインターセプターメソッドには、 `eventData` インターセプトされるイベントに関するコンテキスト情報を提供するパラメーターがあります。 この場合、現在のアプリケーションの DbContext がイベントデータに含まれ、その後、監査メッセージの作成に使用されます。

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

結果として、エンティティ `SaveChangesAudit` のコレクションが `EntityAudit` 挿入、更新、または削除ごとに1つずつ含まれます。 インターセプターは、これらのエンティティを監査データベースに挿入します。

> [!TIP]
> ToString は、イベントの同等のログメッセージを生成するために、すべての EF Core イベントデータクラスでオーバーライドされます。 たとえば、を呼び出すと、 `ContextInitializedEventData.ToString` プロバイダー ' Microsoft. EntityFrameworkCore. Sqlite ' とオプション: None "を使用して、" Entity Framework Core 5.0.0 で初期化された ' ブログ Scontext ' が生成されます。

#### <a name="detecting-success"></a>成功の検出

この監査エンティティはインターセプターに格納されるので、SaveChanges が成功または失敗した場合に再びアクセスできるようになります。 成功した場合は、 `ISaveChangesInterceptor.SavedChanges` <!-- Issue #2748 --> または `ISaveChangesInterceptor.SavedChangesAsync` <!-- Issue #2748 -->  が呼ばれたとき。

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

Audit エンティティはデータベースに既に存在し、更新する必要があるため、監査コンテキストにアタッチされています。 次に、 `Succeeded` とを設定します `EndTime` 。これらのプロパティは変更済みとしてマークされるため、SaveChanges は監査データベースに更新プログラムを送信します。

#### <a name="detecting-failure"></a>検出 (エラーを)

障害は成功とほぼ同じように処理されますが、 `ISaveChangesInterceptor.SaveChangesFailed` <!-- Issue #2748 --> または `ISaveChangesInterceptor.SaveChangesFailedAsync` <!-- Issue #2748 --> メソッドをオーバーライドします。 イベントデータには、スローされた例外が含まれます。

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a>デモンストレーション

[監査サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)には、ブログデータベースを変更し、作成された監査を表示する単純なコンソールアプリケーションが含まれています。

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

結果には、監査データベースの内容が表示されます。

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
