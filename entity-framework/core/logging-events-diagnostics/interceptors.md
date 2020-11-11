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
# <a name="interceptors"></a><span data-ttu-id="d00e3-103">インターセプター</span><span class="sxs-lookup"><span data-stu-id="d00e3-103">Interceptors</span></span>

<span data-ttu-id="d00e3-104">Entity Framework Core (EF Core) インターセプターを使用すると、EF Core 操作のインターセプト、変更、または抑制を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="d00e3-105">これには、コマンドの実行などの低レベルのデータベース操作や、SaveChanges の呼び出しなどの上位レベルの操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="d00e3-106">インターセプターは、傍受される操作の変更または抑制を許可するという点で、ログ記録と診断とは異なります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="d00e3-107">ログ記録の[選択肢とし](xref:core/logging-events-diagnostics/extensions-logging)ては、[単純なログ](xref:core/logging-events-diagnostics/simple-logging)記録や、ログ記録の方が適しています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="d00e3-108">インターセプターは、コンテキストが構成されているときに DbContext インスタンスごとに登録されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="d00e3-109">[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用して、プロセス内のすべての dbcontext インスタンスで同じ情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="d00e3-110">インターセプターの登録</span><span class="sxs-lookup"><span data-stu-id="d00e3-110">Registering interceptors</span></span>

<span data-ttu-id="d00e3-111">インターセプターは <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [、dbcontext インスタンスを構成](xref:core/dbcontext-configuration/index)するときにを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="d00e3-112">これは、通常、のオーバーライドで行われ <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d00e3-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="d00e3-114">`AddInterceptors`または、の一部として、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> または <xref:Microsoft.EntityFrameworkCore.DbContextOptions> dbcontext コンストラクターに渡すインスタンスを作成するときに、を呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="d00e3-115">AddDbContext が使用されている場合、または DbContext コンストラクターに DbContextOptions インスタンスが渡された場合、OnConfiguring は引き続き呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="d00e3-116">これにより、DbContext の構築方法に関係なく、コンテキスト構成を適用するための理想的な場所となります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="d00e3-117">通常、インターセプターはステートレスであるため、1つのインターセプターインスタンスをすべての DbContext インスタンスに使用できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="d00e3-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-118">For example:</span></span>

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

<span data-ttu-id="d00e3-119">すべてのインターセプターインスタンスは、から派生した1つ以上のインターフェイスを実装する必要があり <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> ます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="d00e3-120">各インスタンスは、複数のインターセプトインターフェイスを実装している場合でも、1回だけ登録する必要があります。EF Core は、必要に応じて各インターフェイスのイベントをルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d00e3-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="d00e3-121">データベースの傍受</span><span class="sxs-lookup"><span data-stu-id="d00e3-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="d00e3-122">データベースインターセプトは EF Core 3.0 で追加されたため、リレーショナルデータベースプロバイダーでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-122">Database interception was added in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="d00e3-123">EF Core 5.0 でセーブポイントのサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="d00e3-123">Savepoint support was added in EF Core 5.0.</span></span>

<span data-ttu-id="d00e3-124">低レベルのデータベース傍受は、次の表に示す3つのインターフェイスに分割されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="d00e3-125">Bam</span><span class="sxs-lookup"><span data-stu-id="d00e3-125">Interceptor</span></span>                                                            | <span data-ttu-id="d00e3-126">インターセプトしたデータベース操作</span><span class="sxs-lookup"><span data-stu-id="d00e3-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="d00e3-127">コマンドの作成</span><span class="sxs-lookup"><span data-stu-id="d00e3-127">Creating commands</span></span></br><span data-ttu-id="d00e3-128">コマンドの実行</span><span class="sxs-lookup"><span data-stu-id="d00e3-128">Executing commands</span></span></br><span data-ttu-id="d00e3-129">コマンドの失敗</span><span class="sxs-lookup"><span data-stu-id="d00e3-129">Command failures</span></span></br><span data-ttu-id="d00e3-130">コマンドの DbDataReader を破棄します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="d00e3-131">接続の開始と終了</span><span class="sxs-lookup"><span data-stu-id="d00e3-131">Opening and closing connections</span></span></br><span data-ttu-id="d00e3-132">接続エラー</span><span class="sxs-lookup"><span data-stu-id="d00e3-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="d00e3-133">トランザクションの作成</span><span class="sxs-lookup"><span data-stu-id="d00e3-133">Creating transactions</span></span></br><span data-ttu-id="d00e3-134">既存のトランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="d00e3-134">Using existing transactions</span></span></br><span data-ttu-id="d00e3-135">トランザクションのコミット</span><span class="sxs-lookup"><span data-stu-id="d00e3-135">Committing transactions</span></span></br><span data-ttu-id="d00e3-136">トランザクションのロールバック</span><span class="sxs-lookup"><span data-stu-id="d00e3-136">Rolling back transactions</span></span></br><span data-ttu-id="d00e3-137">セーブポイントの作成と使用</span><span class="sxs-lookup"><span data-stu-id="d00e3-137">Creating and using savepoints</span></span></br><span data-ttu-id="d00e3-138">トランザクションエラー</span><span class="sxs-lookup"><span data-stu-id="d00e3-138">Transaction failures</span></span>

<span data-ttu-id="d00e3-139">基底クラス、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> 、およびには、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> 対応するインターフェイスの各メソッドに対する非 op 実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="d00e3-140">基底クラスを使用して、不要なインターセプトメソッドを実装する必要がないようにします。</span><span class="sxs-lookup"><span data-stu-id="d00e3-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="d00e3-141">各インターセプター型のメソッドはペアになり、データベース操作が開始される前に最初に呼び出され、操作が完了した後に2番目のが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="d00e3-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-142">For example.</span></span> <span data-ttu-id="d00e3-143">たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> クエリが実行される前にが呼び出され、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> クエリがデータベースに送信された後にが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="d00e3-144">メソッドの各ペアには、同期と非同期の両方のバリエーションがあります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="d00e3-145">これにより、アクセストークンの要求などの非同期 i/o を、非同期データベース操作のインターセプトの一部として実行できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="d00e3-146">例: クエリヒントを追加するためのコマンドインターセプト</span><span class="sxs-lookup"><span data-stu-id="d00e3-146">Example: Command interception to add query hints</span></span>

> [!TIP]  
> <span data-ttu-id="d00e3-147">[コマンドインターセプターのサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception)GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="d00e3-148">は、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> データベースに送信される前に SQL を変更するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="d00e3-149">この例では、クエリヒントを含めるように SQL を変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="d00e3-150">多くの場合、インターセプトの難しい部分は、コマンドが変更する必要があるクエリに対応するかどうかを判断することです。</span><span class="sxs-lookup"><span data-stu-id="d00e3-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="d00e3-151">SQL の解析は1つのオプションですが、脆弱になる傾向があります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="d00e3-152">別の方法として、 [EF Core クエリタグ](xref:core/querying/tags) を使用して、変更する各クエリにタグを付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="d00e3-153">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="d00e3-154">このタグは、コマンドテキストの最初の行にコメントとして常に含まれるため、インターセプターで検出できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="d00e3-155">タグを検出すると、クエリ SQL が変更され、適切なヒントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

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

<span data-ttu-id="d00e3-156">注意:</span><span class="sxs-lookup"><span data-stu-id="d00e3-156">Notice:</span></span>

* <span data-ttu-id="d00e3-157">インターセプターは、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> インターセプターインターフェイスにすべてのメソッドを実装する必要がないように、から継承します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="d00e3-158">インターセプターは、同期メソッドと非同期メソッドの両方を実装します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="d00e3-159">これにより、同期クエリと非同期クエリに同じクエリヒントが確実に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="d00e3-160">インターセプターは、生成された `Executing` SQL と共に EF Core によって呼び出されるメソッドを実装し _てから、_ データベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="d00e3-161">これ `Executed` は、データベースの呼び出しが返された後に呼び出されるメソッドと比較します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="d00e3-162">この例のコードを実行すると、クエリにタグが付けられると、次のように生成されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="d00e3-163">一方、クエリにタグが付けられていない場合、そのクエリは変更されずにデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="d00e3-164">例: ADD を使用した SQL Azure 認証の接続の傍受</span><span class="sxs-lookup"><span data-stu-id="d00e3-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]  
> <span data-ttu-id="d00e3-165">[接続インターセプターのサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception)GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="d00e3-166">は <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> 、データベースへの接続に使用する前に、を操作するために使用でき <xref:System.Data.Common.DbConnection> ます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="d00e3-167">これは、Azure Active Directory (AAD) アクセストークンを取得するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="d00e3-168">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-168">For example:</span></span>

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
> <span data-ttu-id="d00e3-169">[Microsoft](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) では、接続文字列を使用して AAD 認証をサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="d00e3-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="d00e3-170">詳細については、「 <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d00e3-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="d00e3-171">接続を開く同期呼び出しが行われた場合、インターセプターはをスローすることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d00e3-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="d00e3-172">これは、アクセストークンを取得するための非同期メソッドがないためです。非同期の [コンテキストから非同期のメソッドを呼び出す方法は、デッドロックのリスクを伴うことなく、汎用的な](https://devblogs.microsoft.com/dotnet/configureawait-faq/)方法ではありません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="d00e3-173">場合によっては、アクセストークンが Azure トークンプロバイダーに自動的にキャッシュされないことがあります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="d00e3-174">要求されたトークンの種類によっては、ここで独自のキャッシュを実装することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="d00e3-175">例: キャッシュ用の高度なコマンドインターセプト</span><span class="sxs-lookup"><span data-stu-id="d00e3-175">Example: Advanced command interception for caching</span></span>

> [!TIP]  
> <span data-ttu-id="d00e3-176">[詳細なコマンドインターセプターサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception)GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="d00e3-177">EF Core インターセプターは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="d00e3-178">インターセプト中の操作の実行を抑制するように EF Core に指示します</span><span class="sxs-lookup"><span data-stu-id="d00e3-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="d00e3-179">に報告された操作の結果を変更 EF Core</span><span class="sxs-lookup"><span data-stu-id="d00e3-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="d00e3-180">この例は、これらの機能を使用して、プリミティブな第2レベルのキャッシュのように動作するインターセプターを示しています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="d00e3-181">キャッシュされたクエリ結果が特定のクエリに対して返され、データベースのラウンドトリップを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="d00e3-182">この方法で EF Core の既定の動作を変更する場合は注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="d00e3-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="d00e3-183">EF Core は、正常に処理できない異常な結果が返された場合に予期しない方法で動作することがあります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="d00e3-184">また、この例ではインターセプターの概念を示しています。これは、堅牢な第2レベルのキャッシュ実装のテンプレートとしては意図されていません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="d00e3-185">この例では、アプリケーションは多くの場合、最新の "毎日のメッセージ" を取得するクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="d00e3-186">このクエリは、インターセプターで簡単に検出できるように [タグ付け](xref:core/querying/tags) されています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="d00e3-187">これは、データベースに対して、毎日1回だけ新しいメッセージを照会することです。</span><span class="sxs-lookup"><span data-stu-id="d00e3-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="d00e3-188">それ以外の場合、アプリケーションはキャッシュされた結果を使用します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="d00e3-189">(サンプルでは、新しい日をシミュレートするために、サンプルで10秒の遅延を使用しています)。</span><span class="sxs-lookup"><span data-stu-id="d00e3-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="d00e3-190">インターセプターの状態</span><span class="sxs-lookup"><span data-stu-id="d00e3-190">Interceptor state</span></span>

<span data-ttu-id="d00e3-191">このインターセプターはステートフルなので、クエリが実行された最新の毎日のメッセージの ID とメッセージテキストが格納されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="d00e3-192">この状態により、キャッシュでは同じインターセプターを複数のコンテキストインスタンスで使用する必要があるため、 [ロック](/dotnet/csharp/language-reference/keywords/lock-statement) も必要になります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="d00e3-193">実行前</span><span class="sxs-lookup"><span data-stu-id="d00e3-193">Before execution</span></span>

<span data-ttu-id="d00e3-194">メソッドで `Executing` (つまり、データベース呼び出しを行う前)、インターセプターはタグ付きクエリを検出し、キャッシュされた結果があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="d00e3-195">そのような結果が見つかった場合は、クエリが抑制され、キャッシュされた結果が代わりに使用されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

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

<span data-ttu-id="d00e3-196">コードによってが呼び出され、キャッシュされ <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> たデータを含む置換が渡されることに注意して <xref:System.Data.Common.DbDataReader> ください。</span><span class="sxs-lookup"><span data-stu-id="d00e3-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="d00e3-197">この InterceptionResult が返され、クエリの実行が抑制されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="d00e3-198">代わりに、クエリの結果として EF Core によって置換リーダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="d00e3-199">このインターセプターは、コマンドテキストも操作します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="d00e3-200">この操作は必要ありませんが、ログメッセージのわかりやすさが向上しています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="d00e3-201">クエリが実行されないため、コマンドテキストが有効な SQL である必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="d00e3-202">実行後</span><span class="sxs-lookup"><span data-stu-id="d00e3-202">After execution</span></span>

<span data-ttu-id="d00e3-203">キャッシュされたメッセージがない場合、または有効期限が切れている場合、上記のコードでは結果が抑制されません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="d00e3-204">そのため、EF Core は通常どおりにクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="d00e3-205">その後、実行後にインターセプターの `Executed` メソッドに戻ります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="d00e3-206">この時点で、結果がまだキャッシュされたリーダーでない場合、新しいメッセージ ID と文字列は実際のリーダーから exacted され、このクエリの次回の使用に備えてキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="d00e3-207">デモンストレーション</span><span class="sxs-lookup"><span data-stu-id="d00e3-207">Demonstration</span></span>

<span data-ttu-id="d00e3-208">[キャッシュインターセプターサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception)には、キャッシュをテストするための毎日のメッセージを照会する単純なコンソールアプリケーションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

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

<span data-ttu-id="d00e3-209">次の出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-209">This results in the following output:</span></span>

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

<span data-ttu-id="d00e3-210">ログ出力から、タイムアウトが経過するまでアプリケーションがキャッシュされたメッセージを使用し続けていることがわかります。この時点で、新しいメッセージに対してデータベースに対してクエリが再度実行されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="d00e3-211">SaveChanges インターセプト</span><span class="sxs-lookup"><span data-stu-id="d00e3-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="d00e3-212">SaveChanges インターセプトが EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="d00e3-212">SaveChanges interception was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="d00e3-213">[SaveChanges インターセプターサンプルは](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="d00e3-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> および <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> インターセプトポイントは、 `ISaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="d00e3-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the `ISaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-215">efi.</span><span class="sxs-lookup"><span data-stu-id="d00e3-215">interface.</span></span> <span data-ttu-id="d00e3-216">他のインターセプターと同様、 `SaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="d00e3-216">As for other interceptors, the `SaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-217">非 op メソッドを含む基本クラスは、便宜上提供されています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-217">base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="d00e3-218">インターセプターは強力です。</span><span class="sxs-lookup"><span data-stu-id="d00e3-218">Interceptors are powerful.</span></span> <span data-ttu-id="d00e3-219">ただし、多くの場合、SaveChanges メソッドをオーバーライドするか、DbContext で公開されている [savechanges に .net イベント](xref:core/logging-events-diagnostics/events) を使用する方が簡単な場合があります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-219">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="d00e3-220">例: 監査のための SaveChanges インターセプト</span><span class="sxs-lookup"><span data-stu-id="d00e3-220">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="d00e3-221">SaveChanges をインターセプトして、加えられた変更の独立した監査レコードを作成できます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-221">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="d00e3-222">これは、堅牢な監査ソリューションとしては想定されていません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-222">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="d00e3-223">むしろ、インターセプトの機能を示すために使用される単純な例です。</span><span class="sxs-lookup"><span data-stu-id="d00e3-223">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="d00e3-224">アプリケーションコンテキスト</span><span class="sxs-lookup"><span data-stu-id="d00e3-224">The application context</span></span>

<span data-ttu-id="d00e3-225">[監査のサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)では、ブログと投稿を含む単純な dbcontext を使用します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-225">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

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

<span data-ttu-id="d00e3-226">各 DbContext インスタンスに対して、インターセプターの新しいインスタンスが登録されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d00e3-226">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="d00e3-227">これは、監査インターセプターには、現在のコンテキストインスタンスにリンクされている状態が含まれているためです。</span><span class="sxs-lookup"><span data-stu-id="d00e3-227">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="d00e3-228">監査コンテキスト</span><span class="sxs-lookup"><span data-stu-id="d00e3-228">The audit context</span></span>

<span data-ttu-id="d00e3-229">このサンプルには、監査データベースに使用される2つ目の DbContext と model も含まれています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-229">The sample also contains a second DbContext and model used for the auditing database.</span></span>

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

#### <a name="the-interceptor"></a><span data-ttu-id="d00e3-230">インターセプター</span><span class="sxs-lookup"><span data-stu-id="d00e3-230">The interceptor</span></span>

<span data-ttu-id="d00e3-231">インターセプターを使用した監査に関する一般的な考え方は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d00e3-231">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="d00e3-232">SaveChanges の先頭に監査メッセージが作成され、監査データベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-232">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="d00e3-233">SaveChanges を続行できます</span><span class="sxs-lookup"><span data-stu-id="d00e3-233">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="d00e3-234">SaveChanges が成功すると、監査メッセージが更新され、成功を示すメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-234">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="d00e3-235">SaveChanges が失敗した場合は、失敗を示す監査メッセージが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-235">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="d00e3-236">最初の段階は、の上書きを使用してデータベースに変更が送信される前に処理されます。 `ISaveChangesInterceptor.SavingChanges`</span><span class="sxs-lookup"><span data-stu-id="d00e3-236">The first stage is handled before any changes are sent to the database using overrides of `ISaveChangesInterceptor.SavingChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-237">そして `ISaveChangesInterceptor.SavingChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="d00e3-237">and `ISaveChangesInterceptor.SavingChangesAsync`</span></span><!-- Issue #2748 --><span data-ttu-id="d00e3-238">.</span><span class="sxs-lookup"><span data-stu-id="d00e3-238">.</span></span>

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

<span data-ttu-id="d00e3-239">同期と非同期の両方のメソッドをオーバーライドすると、SaveChanges または SaveChangesAsync が呼び出されているかどうかにかかわらず、監査が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-239">Overriding both sync and async methods ensures that auditing will happen regardless of whether SaveChanges or SaveChangesAsync are called.</span></span> <span data-ttu-id="d00e3-240">非同期のオーバーロードは、監査データベースに対して非ブロッキング非同期 i/o を実行できることにも注意してください。</span><span class="sxs-lookup"><span data-stu-id="d00e3-240">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="d00e3-241">すべてのデータベース i/o が非同期であることを確認するために、sync SavingChanges メソッドからをスローすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d00e3-241">You may wish to throw from the sync SavingChanges method to ensure that all database I/O is async.</span></span> <span data-ttu-id="d00e3-242">この場合、アプリケーションは常に SaveChangesAsync を呼び出し、SaveChanges を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d00e3-242">This then requires that the application always calls SaveChangesAsync and never SaveChanges.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="d00e3-243">監査メッセージ</span><span class="sxs-lookup"><span data-stu-id="d00e3-243">The audit message</span></span>

<span data-ttu-id="d00e3-244">すべてのインターセプターメソッドには、 `eventData` インターセプトされるイベントに関するコンテキスト情報を提供するパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-244">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="d00e3-245">この場合、現在のアプリケーションの DbContext がイベントデータに含まれ、その後、監査メッセージの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-245">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

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

<span data-ttu-id="d00e3-246">結果として、エンティティ `SaveChangesAudit` のコレクションが `EntityAudit` 挿入、更新、または削除ごとに1つずつ含まれます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-246">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="d00e3-247">インターセプターは、これらのエンティティを監査データベースに挿入します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-247">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="d00e3-248">ToString は、イベントの同等のログメッセージを生成するために、すべての EF Core イベントデータクラスでオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-248">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="d00e3-249">たとえば、を呼び出すと、 `ContextInitializedEventData.ToString` プロバイダー ' Microsoft. EntityFrameworkCore. Sqlite ' とオプション: None "を使用して、" Entity Framework Core 5.0.0 で初期化された ' ブログ Scontext ' が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-249">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="d00e3-250">成功の検出</span><span class="sxs-lookup"><span data-stu-id="d00e3-250">Detecting success</span></span>

<span data-ttu-id="d00e3-251">この監査エンティティはインターセプターに格納されるので、SaveChanges が成功または失敗した場合に再びアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="d00e3-251">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="d00e3-252">成功した場合は、 `ISaveChangesInterceptor.SavedChanges`</span><span class="sxs-lookup"><span data-stu-id="d00e3-252">For success, `ISaveChangesInterceptor.SavedChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-253">または `ISaveChangesInterceptor.SavedChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="d00e3-253">or `ISaveChangesInterceptor.SavedChangesAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-254"> が呼ばれたとき。</span><span class="sxs-lookup"><span data-stu-id="d00e3-254">is called.</span></span>

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

<span data-ttu-id="d00e3-255">Audit エンティティはデータベースに既に存在し、更新する必要があるため、監査コンテキストにアタッチされています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-255">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="d00e3-256">次に、 `Succeeded` とを設定します `EndTime` 。これらのプロパティは変更済みとしてマークされるため、SaveChanges は監査データベースに更新プログラムを送信します。</span><span class="sxs-lookup"><span data-stu-id="d00e3-256">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="d00e3-257">検出 (エラーを)</span><span class="sxs-lookup"><span data-stu-id="d00e3-257">Detecting failure</span></span>

<span data-ttu-id="d00e3-258">障害は成功とほぼ同じように処理されますが、 `ISaveChangesInterceptor.SaveChangesFailed`</span><span class="sxs-lookup"><span data-stu-id="d00e3-258">Failure is handled in much the same way as success, but in the `ISaveChangesInterceptor.SaveChangesFailed`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-259">または `ISaveChangesInterceptor.SaveChangesFailedAsync`</span><span class="sxs-lookup"><span data-stu-id="d00e3-259">or `ISaveChangesInterceptor.SaveChangesFailedAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="d00e3-260">メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d00e3-260">method.</span></span> <span data-ttu-id="d00e3-261">イベントデータには、スローされた例外が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-261">The event data contains the exception that was thrown.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="d00e3-262">デモンストレーション</span><span class="sxs-lookup"><span data-stu-id="d00e3-262">Demonstration</span></span>

<span data-ttu-id="d00e3-263">[監査サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)には、ブログデータベースを変更し、作成された監査を表示する単純なコンソールアプリケーションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d00e3-263">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

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

<span data-ttu-id="d00e3-264">結果には、監査データベースの内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d00e3-264">The result shows the contents of the auditing database:</span></span>

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
