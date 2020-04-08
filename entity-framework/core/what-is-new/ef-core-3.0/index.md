---
title: Entity Framework Core 3.0 の新機能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ebc676930ffc396aa70bb8afb91cf5a0cd43e04d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413197"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="cff45-102">Entity Framework Core 3.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="cff45-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="cff45-103">以下のリストには、EF Core 3.0 の主な新機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cff45-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="cff45-104">メジャー リリースである EF Core 3.0 には、いくつかの[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)も含まれています。これは、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化です。</span><span class="sxs-lookup"><span data-stu-id="cff45-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="cff45-105">LINQ の見直し</span><span class="sxs-lookup"><span data-stu-id="cff45-105">LINQ overhaul</span></span>

<span data-ttu-id="cff45-106">LINQ を使うと、任意の .NET 言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを提供できます。</span><span class="sxs-lookup"><span data-stu-id="cff45-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="cff45-107">しかし、LINQ では、任意の式 (メソッド呼び出しまたは操作) を含む複雑なクエリを無制限に記述できます。</span><span class="sxs-lookup"><span data-stu-id="cff45-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="cff45-108">これらの組み合わせをすべて処理する方法は、LINQ プロバイダーの主要な課題です。</span><span class="sxs-lookup"><span data-stu-id="cff45-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="cff45-109">EF Core 3.0 では、LINQ プロバイダーが再設計され、より多くのクエリ パターンの SQL への変換、より多くのケースでの効率的なクエリの生成、非効率的なクエリの未検出の防止が可能になりました。</span><span class="sxs-lookup"><span data-stu-id="cff45-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="cff45-110">新しい LINQ プロバイダーは、既存のアプリケーションやデータ プロバイダーを破壊することなく、今後のリリースで新しいクエリ機能やパフォーマンスの改善を提供できるようにするための基盤です。</span><span class="sxs-lookup"><span data-stu-id="cff45-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="cff45-111">制限付きクライアント評価</span><span class="sxs-lookup"><span data-stu-id="cff45-111">Restricted client evaluation</span></span>

<span data-ttu-id="cff45-112">最も重要な設計上の変更点は、パラメーターに変換できない、または SQL に変換できない LINQ 式の処理方法に関するものです。</span><span class="sxs-lookup"><span data-stu-id="cff45-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="cff45-113">以前のバージョンの EF Core では、SQL に変換できるクエリの部分を特定し、残りのクエリをクライアント上で実行していました。</span><span class="sxs-lookup"><span data-stu-id="cff45-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="cff45-114">この種のクライアント側の実行は、状況によっては望ましい場合がありますが、その他の多くのケースでは非効率的なクエリが発生する場合があります。</span><span class="sxs-lookup"><span data-stu-id="cff45-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="cff45-115">たとえば、EF Core 2.2 では、`Where()` 呼び出し内で述語を変換できなかった場合、フィルターなしで SQL ステートメントが実行され、すべての行がデータベースから転送された後、メモリ内でフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="cff45-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="cff45-116">これは、データベースに少数の行しか含まれていない場合には許容できる可能性がありますが、データベースに多数の行が含まれている場合は、パフォーマンスに大きな問題が発生したり、アプリケーションに障害が発生したりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cff45-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number of rows.</span></span>

<span data-ttu-id="cff45-117">EF Core 3.0 では、クライアント評価は最上位レベルのプロジェクション (基本的には `Select()` への最後の呼び出し) でのみ発生するように制限されました。</span><span class="sxs-lookup"><span data-stu-id="cff45-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="cff45-118">EF Core 3.0 で、クエリ内の他の場所では変換できない式が検出された場合、ランタイム例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cff45-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="cff45-119">前の例のように述語の条件をクライアント上で評価するには、開発者がクエリの評価を LINQ to Objects に明示的に切り替えることが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="cff45-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="cff45-120">これが既存のアプリケーションに与える可能性がある影響について詳しくは、[破壊的変更に関するドキュメント](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cff45-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="cff45-121">LINQ クエリごとに 1 つの SQL ステートメント</span><span class="sxs-lookup"><span data-stu-id="cff45-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="cff45-122">3\.0 で大幅に変更された設計のもう 1 つの側面は、LINQ クエリごとに常に 1 つの SQL ステートメントが生成されるようになったことです。</span><span class="sxs-lookup"><span data-stu-id="cff45-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="cff45-123">以前のバージョンでは、コレクション ナビゲーション プロパティで `Include()` 呼び出しを変換する場合や、サブクエリを含む特定のパターンに従ったクエリを変換する場合など、場合によっては複数の SQL ステートメントが生成されていました。</span><span class="sxs-lookup"><span data-stu-id="cff45-123">In previous versions, we used to generate multiple SQL statements in certain cases, translated `Include()` calls on collection navigation properties and translated queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="cff45-124">これが便利なケースもいくつかあり、`Include()` の場合はネットワーク経由での冗長データの送信を回避するのにも役立ちました。しかし、実装が複雑になり、いくつかの非常に非効率的な動作 (N+1 クエリ) が発生しました。</span><span class="sxs-lookup"><span data-stu-id="cff45-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, and it resulted in some extremely inefficient behaviors (N+1 queries).</span></span> <span data-ttu-id="cff45-125">複数のクエリ間で返されたデータに、矛盾が生じる可能性がある状況が発生しました。</span><span class="sxs-lookup"><span data-stu-id="cff45-125">There were situations in which the data returned across multiple queries was potentially inconsistent.</span></span>

<span data-ttu-id="cff45-126">クライアント評価と同様に、EF Core 3.0 で LINQ クエリを 1 つの SQL ステートメントに変換できなかった場合は、ランタイム例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cff45-126">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="cff45-127">ただし、EF Core では、以前は複数のクエリが生成されていた一般的なパターンの多くを、JOIN を使って 1 つのクエリに変換できるようになっています。</span><span class="sxs-lookup"><span data-stu-id="cff45-127">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="cff45-128">Cosmos DB のサポート</span><span class="sxs-lookup"><span data-stu-id="cff45-128">Cosmos DB support</span></span>

<span data-ttu-id="cff45-129">EF Core 用の Cosmos DB プロバイダーでは、EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにします。</span><span class="sxs-lookup"><span data-stu-id="cff45-129">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="cff45-130">その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="cff45-130">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="cff45-131">プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="cff45-131">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="cff45-132">詳細については、[Cosmos DB プロバイダーに関するドキュメント](xref:core/providers/cosmos/index)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cff45-132">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="cff45-133">C# 8.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="cff45-133">C# 8.0 support</span></span>

<span data-ttu-id="cff45-134">EF Core 3.0 では、次のいくつかの [C# 8.0 の新機能](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8)が利用されています。</span><span class="sxs-lookup"><span data-stu-id="cff45-134">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="cff45-135">非同期ストリーム</span><span class="sxs-lookup"><span data-stu-id="cff45-135">Asynchronous streams</span></span>

<span data-ttu-id="cff45-136">非同期クエリの結果は、新しい標準 `IAsyncEnumerable<T>` インターフェイスを使用して公開されるようになり、`await foreach` を使用して使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="cff45-136">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

<span data-ttu-id="cff45-137">詳細については、[C# の非同期ストリームに関するドキュメント](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cff45-137">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="cff45-138">null 許容参照型</span><span class="sxs-lookup"><span data-stu-id="cff45-138">Nullable reference types</span></span>

<span data-ttu-id="cff45-139">コード内でこの新しい機能を有効にした場合、EF Core によって参照型プロパティの null 値の許容が調べられ、データベース内の対応する列およびリレーションシップにそれが適用されます。null 非許容参照型のプロパティは、`[Required]` データ注釈属性が含まれているかのように処理されます。</span><span class="sxs-lookup"><span data-stu-id="cff45-139">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="cff45-140">たとえば、次のクラスでは、型 `string?` としてマークされたプロパティは省略可能として構成されますが、`string` は必須として構成されます。</span><span class="sxs-lookup"><span data-stu-id="cff45-140">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

<span data-ttu-id="cff45-141">詳細については、EF Core ドキュメントの「[null 許容参照型の使用](xref:core/miscellaneous/nullable-reference-types)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cff45-141">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="cff45-142">データベース操作のインターセプト</span><span class="sxs-lookup"><span data-stu-id="cff45-142">Interception of database operations</span></span>

<span data-ttu-id="cff45-143">EF Core 3.0 の新しいインターセプト API を使うと、EF Core の通常操作の一部として低レベルのデータベース操作が発生するたびに、カスタム ロジックの提供が自動的に呼び出されるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="cff45-143">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="cff45-144">たとえば、接続を開いたり、トランザクションをコミットしたり、コマンドを実行したりする場合です。</span><span class="sxs-lookup"><span data-stu-id="cff45-144">For example, when opening connections, committing transactions, or executing commands.</span></span>

<span data-ttu-id="cff45-145">EF 6 に存在していたインターセプト機能と同様に、インターセプターによって、操作の発生前または発生後にそれをインターセプトすることができます。</span><span class="sxs-lookup"><span data-stu-id="cff45-145">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="cff45-146">操作の発生前にインターセプトした場合は、実行を回避し、インターセプトのロジックから代わりの結果を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cff45-146">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span>

<span data-ttu-id="cff45-147">たとえば、コマンド テキストを操作するために、`IDbCommandInterceptor` を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="cff45-147">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

<span data-ttu-id="cff45-148">次に、 `DbContext` にこれを登録します。</span><span class="sxs-lookup"><span data-stu-id="cff45-148">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="cff45-149">データベース ビューのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="cff45-149">Reverse engineering of database views</span></span>

<span data-ttu-id="cff45-150">クエリ型 (データベースから読み取ることはできても更新は行えないデータを表す) は、[キーなしエンティティ型](xref:core/modeling/keyless-entity-types)に名前が変更されました。</span><span class="sxs-lookup"><span data-stu-id="cff45-150">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span>
<span data-ttu-id="cff45-151">それはほとんどのシナリオでデータベース ビューのマッピングに対して最適であるため、データベース ビューのリバース エンジニアリングを行う場合に、EF Core によってキーなしエンティティ型が自動的に作成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="cff45-151">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="cff45-152">たとえば、[dotnet ef コマンドライン ツール](xref:core/miscellaneous/cli/dotnet)を使って次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="cff45-152">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="cff45-153">すると、ツールによって、キーなしのビューとテーブルの型が自動的にスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="cff45-153">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="cff45-154">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="cff45-154">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="cff45-155">EF Core 3.0 以降では、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`Order` なしで `OrderDetails` を追加することができるようになり、主キー以外のすべての `OrderDetails` プロパティは NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="cff45-155">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="cff45-156">クエリ時には、必須プロパティのいずれかに値がない場合、または主キー以外に必須プロパティがなく、すべてのプロパティが `OrderDetails` である場合、EF Core によって `null` が `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="cff45-156">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="cff45-157">.NET Core での EF 6.3</span><span class="sxs-lookup"><span data-stu-id="cff45-157">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="cff45-158">これは EF Core 3.0 の機能ではありませんが、現在のお客様の多くにとって重要であると考えられます。</span><span class="sxs-lookup"><span data-stu-id="cff45-158">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span>

<span data-ttu-id="cff45-159">多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると、多大な労力が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cff45-159">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span>
<span data-ttu-id="cff45-160">そのため、.NET Core 3.0 上で実行できるように最新バージョンの EF 6 を移植することに決定しました。</span><span class="sxs-lookup"><span data-stu-id="cff45-160">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span>

<span data-ttu-id="cff45-161">詳細については、「[EF 6 の新機能](xref:ef6/what-is-new/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cff45-161">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="cff45-162">延期された機能</span><span class="sxs-lookup"><span data-stu-id="cff45-162">Postponed features</span></span>

<span data-ttu-id="cff45-163">EF Core 3.0 向けに最初に計画された一部の機能は、今後のリリースに延期されました。</span><span class="sxs-lookup"><span data-stu-id="cff45-163">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="cff45-164">移行でモデルの一部を無視する機能。[#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) として追跡されています。</span><span class="sxs-lookup"><span data-stu-id="cff45-164">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="cff45-165">プロパティ バッグのエンティティ。2 つの異なるイシューとして追跡されています: 共有型のエンティティに関する [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) と、インデックス付きプロパティ マッピングのサポートに関する [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。</span><span class="sxs-lookup"><span data-stu-id="cff45-165">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
