---
title: EF Core 3.0 の新機能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: 528733d6eec33de2c9538541a6ed5be704b9d433
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005553"
---
# <a name="new-features-included-in-ef-core-30"></a><span data-ttu-id="20bf2-102">EF Core 3.0 に含まれる新機能</span><span class="sxs-lookup"><span data-stu-id="20bf2-102">New features included in EF Core 3.0</span></span>

<span data-ttu-id="20bf2-103">以下のリストには、EF Core 3.0 について計画されている主な新機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="20bf2-103">The following list includes the major new features planned for EF Core 3.0.</span></span>

<span data-ttu-id="20bf2-104">EF Core 3.0 はメジャー リリースであり、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化である、[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)も多く含まれています。</span><span class="sxs-lookup"><span data-stu-id="20bf2-104">EF Core 3.0 is a major release and also contains numerous [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-improvements"></a><span data-ttu-id="20bf2-105">LINQ の機能強化</span><span class="sxs-lookup"><span data-stu-id="20bf2-105">LINQ improvements</span></span> 

<span data-ttu-id="20bf2-106">LINQ を使うと、好みの言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを提供できます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-106">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="20bf2-107">しかし、LINQ では、任意の式 (メソッド呼び出しまたは操作) を含む複雑なクエリを無制限に記述できます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="20bf2-108">これらのすべての組み合わせを処理することは、LINQ プロバイダーにとって以前よりかなり困難でした。</span><span class="sxs-lookup"><span data-stu-id="20bf2-108">Handling all those combinations has always been a significant challenge for LINQ providers.</span></span>
<span data-ttu-id="20bf2-109">EF Core 3.0 では、より多くの式での SQL への変換、より多くのケースでの効率的なクエリの生成を可能にし、非効率的なクエリが検出されないことがないようにし、既存のアプリケーションやデータ プロバイダーを中断させることなく、新しいクエリの機能とパフォーマンスの強化を徐々に導入しやすくなるように、LINQ の実装を書き直しました。</span><span class="sxs-lookup"><span data-stu-id="20bf2-109">In EF Core 3.0, we've rewritten our LINQ implementation to enable translating more expressions into SQL, to generate efficient queries in more cases, to prevent inefficient queries from going undetected, and to make it easier for us to gradually introduce new query capabilities and performance improvementswithout breaking existing applications and data providers.</span></span>

### <a name="client-evaluation"></a><span data-ttu-id="20bf2-110">クライアントの評価</span><span class="sxs-lookup"><span data-stu-id="20bf2-110">Client evaluation</span></span>

<span data-ttu-id="20bf2-111">EF Core 3.0 の主な設計変更では、SQL またはパラメーターに変換できない LINQ 式の処理方法に関連があります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-111">The main design change in EF Core 3.0 has to do with how it handles LINQ expressions that it cannot translate to SQL or parameters:</span></span>

<span data-ttu-id="20bf2-112">最初のいくつかのバージョンの EF Core では、SQL に変換できるクエリの部分とクエリの残りの部分をクライアントに対して実行できる部分を特定するだけです。</span><span class="sxs-lookup"><span data-stu-id="20bf2-112">In the first few versions, EF Core simply figured out what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="20bf2-113">状況によっては、このクライアント側の実行の種類が望ましいものとなる場合がありますが、その他の多くの場合では、非効率なクエリが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-113">This type of client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>
<span data-ttu-id="20bf2-114">たとえば、EF Core 2.2 では `Where()` 呼び出しで述語を変換できなかった場合、フィルターを使用せずに SQL ステートメントを実行し、データベースの行をすべて読み取り、メモリ内でフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="20bf2-114">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed a SQL statement without a filter, read all all the rows from the database, and then filtered them in-memory.</span></span>
<span data-ttu-id="20bf2-115">これは、データベースに少数の行が含まれている場合に許容される可能性がありますが、データベースの行が多い場合は、パフォーマンスに大きな問題が発生したり、アプリケーションのエラーが発生したりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-115">That may be acceptable if the database contains a small number of rows, but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>
<span data-ttu-id="20bf2-116">EF Core 3.0 では、最上位レベルのプロジェクション (`Select()` への最後の呼び出し) でのみ発生するように、クライアント評価を制限しています。</span><span class="sxs-lookup"><span data-stu-id="20bf2-116">In EF Core 3.0 we have restricted client evaluation to only happen on the top-level projection (the last call to `Select()`).</span></span>
<span data-ttu-id="20bf2-117">EF Core 3.0 でクエリ内の他の場所では変換できない式を検出した場合、ランタイムの例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-117">When EF Core 3.0 detects expressions that cannot be translated anywhere else in the query, it throws a runtime exception.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="20bf2-118">Cosmos DB のサポート</span><span class="sxs-lookup"><span data-stu-id="20bf2-118">Cosmos DB support</span></span> 

<span data-ttu-id="20bf2-119">EF Core 用の Cosmos DB プロバイダーでは、EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにします。</span><span class="sxs-lookup"><span data-stu-id="20bf2-119">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="20bf2-120">その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="20bf2-120">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="20bf2-121">プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-121">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="20bf2-122">C# 8.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="20bf2-122">C# 8.0 support</span></span>

<span data-ttu-id="20bf2-123">EF Core 3.0 では、C# 8.0 の新機能の一部を利用します。</span><span class="sxs-lookup"><span data-stu-id="20bf2-123">EF Core 3.0 takes advantage of some of the new features in C# 8.0:</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="20bf2-124">非同期ストリーム</span><span class="sxs-lookup"><span data-stu-id="20bf2-124">Asynchronous streams</span></span>

<span data-ttu-id="20bf2-125">非同期クエリの結果は、新しい標準 `IAsyncEnumerable<T>` インターフェイスを使用して公開されるようになり、`await foreach` を使用して使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="20bf2-125">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Proccess(o);
} 
```

### <a name="nullable-reference-types"></a><span data-ttu-id="20bf2-126">null 許容参照型</span><span class="sxs-lookup"><span data-stu-id="20bf2-126">Nullable reference types</span></span> 

<span data-ttu-id="20bf2-127">コード内でこの新しい機能が有効になっている場合、EF Core はデータベース内の列およびリレーションシップの NULL 値の許容を決定するために、参照型 (文字列プロパティやナビゲーション プロパティなどのプリミティブ型) のプロパティの NULL 値の許容を判断できます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-127">When this new feature is enabled in your code, EF Core can reason about the nullability of properties of refrence types (either of primitive types like string or navigation properties) to decide the nullability of columns and relationships in the database.</span></span>

## <a name="interception"></a><span data-ttu-id="20bf2-128">interception</span><span class="sxs-lookup"><span data-stu-id="20bf2-128">Interception</span></span>

<span data-ttu-id="20bf2-129">EF Core 3.0 の新しいインターセプト API を使用すると、接続を開いたり、トランザクションを開始したり、コマンドを実行したりするなど、EF Core の通常の動作の一部として実行される低レベルのデータベース操作の結果をプログラムによって監視および変更できます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-129">The new interception API in EF Core 3.0 allows programatically observing and modifying the outcome of low-level database operations that occur as part of the normal operation of EF Core, such as opening connections, initating transactions, and executing commands.</span></span> 

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="20bf2-130">データベース ビューのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="20bf2-130">Reverse engineering of database views</span></span>

<span data-ttu-id="20bf2-131">キーなしのエンティティ型 (以前の[クエリ型](xref:core/modeling/query-types)) は、データベースから読み取ることができるデータを表しますが、更新はできません。</span><span class="sxs-lookup"><span data-stu-id="20bf2-131">Entity types without keys (previously known as [query types](xref:core/modeling/query-types)) represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="20bf2-132">この特性によって、ほとんどのシナリオのマッピング データベース ビューに最適なものとなるため、データベース ビューのリバース エンジニアリング時にキーなしのエンティティ型の作成を自動化しました。</span><span class="sxs-lookup"><span data-stu-id="20bf2-132">This characteristic makes them an excellent fit for mapping database views in most scenarios, so we automated the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="20bf2-133">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="20bf2-133">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="20bf2-134">EF Core 3.0 以降では、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` なしで `Order` を追加することができるようになり、主キー以外のすべての `OrderDetails` プロパティは NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-134">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="20bf2-135">クエリ時には、必須プロパティのいずれかに値がない場合、または主キー以外に必須プロパティがなく、すべてのプロパティが `null` である場合、EF Core によって `OrderDetails` が `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-135">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

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

## <a name="ef-63-on-net-core"></a><span data-ttu-id="20bf2-136">.NET Core での EF 6.3</span><span class="sxs-lookup"><span data-stu-id="20bf2-136">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="20bf2-137">多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると多大な労力が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-137">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="20bf2-138">そのため、.NET Core 3.0 上で最新バージョンの EF 6 を実行できるようにしました。</span><span class="sxs-lookup"><span data-stu-id="20bf2-138">For that reason, we have enabled the newewst version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="20bf2-139">次のように、いくつかの制約があります。</span><span class="sxs-lookup"><span data-stu-id="20bf2-139">There are some limitations, for example:</span></span>
- <span data-ttu-id="20bf2-140">.NET Core での作業に新しいプロバイダーが必要です</span><span class="sxs-lookup"><span data-stu-id="20bf2-140">New providers are required to work on .NET Core</span></span>
- <span data-ttu-id="20bf2-141">SQL Server での空間サポートは有効になりません</span><span class="sxs-lookup"><span data-stu-id="20bf2-141">Spatial support with SQL Server won't be enabled</span></span>

## <a name="postponed-features"></a><span data-ttu-id="20bf2-142">延期された機能</span><span class="sxs-lookup"><span data-stu-id="20bf2-142">Postponed features</span></span>

<span data-ttu-id="20bf2-143">EF Core 3.0 向けに最初に計画された一部の機能は、今後のリリースに延期されました。</span><span class="sxs-lookup"><span data-stu-id="20bf2-143">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span> 

- <span data-ttu-id="20bf2-144">移行でモデルのパーツを無視する機能。[#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725) によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="20bf2-144">Ability to ingore parts of a model in migrations, tracked by [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="20bf2-145">2 つの異なる問題によって追跡されるプロパティ バッグ エンティティ。共有型のエンティティに関する [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) と、インデックス付きプロパティのマッピング サポートに関する [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。</span><span class="sxs-lookup"><span data-stu-id="20bf2-145">Property bag entities, tracked by two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
