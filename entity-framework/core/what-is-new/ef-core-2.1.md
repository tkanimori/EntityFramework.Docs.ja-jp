---
title: "EF Core 2.1 の新機能 - EF Core"
author: divega
ms.author: divega
ms.date: 2/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 1e5e9839bae1e5da4082d90c02d098bb3b2b43bd
ms.sourcegitcommit: 4b7d3d3e258b0d9cb778bb45a9f4a33c0792e38e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="afc9c-102">EF Core 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="afc9c-102">New features in EF Core 2.1</span></span>
> [!NOTE]  
> <span data-ttu-id="afc9c-103">このリリースはまだプレビュー段階です。</span><span class="sxs-lookup"><span data-stu-id="afc9c-103">This release is still in preview.</span></span>

<span data-ttu-id="afc9c-104">数多くの小さな機能強化と 100 を超える製品のバグ修正の他に、EF Core 2.1 にはいくつかの新機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="afc9c-104">Besides numerous small improvements and more than a hundred product bug fixes, EF Core 2.1 includes several new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="afc9c-105">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="afc9c-105">Lazy loading</span></span>
<span data-ttu-id="afc9c-106">EF Core に、すべてのユーザーが自身のナビゲーション プロパティを必要なときに読み込むことができるエンティティ クラスを作成するために必要なビルド ブロックが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-106">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="afc9c-107">また、これらのビルド ブロックを利用して、最小限の変更を行ったエンティティ クラス (仮想ナビゲーション プロパティが指定されたクラスなど) に基づいて遅延読み込みを生成する新しいパッケージ Microsoft.EntityFrameworkCore.Proxies も作成しました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-107">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (e.g. classes with virtual navigation properties).</span></span>

<span data-ttu-id="afc9c-108">このトピックの詳細については、[遅延読み込みのセクション](xref:core/querying/related-data#lazy-loading)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-108">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="afc9c-109">エンティティ コンストラクターのパラメーター</span><span class="sxs-lookup"><span data-stu-id="afc9c-109">Parameters in entity constructors</span></span>
<span data-ttu-id="afc9c-110">遅延読み込みに必要なビルド ブロックの 1 つとして、コンストラクターでパラメーターを受け取るエンティティを作成できるようにしました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-110">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="afc9c-111">パラメーターを使用して、プロパティ値、遅延読み込みデリゲート、およびサービスを挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-111">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="afc9c-112">このトピックの詳細については、[パラメーターが指定されたエンティティ コンストラクターに関するセクション](xref:core/modeling/constructors)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-112">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="afc9c-113">値変換</span><span class="sxs-lookup"><span data-stu-id="afc9c-113">Value conversions</span></span>
<span data-ttu-id="afc9c-114">これまで、EF Core では、基になるデータベース プロバイダーでネイティブにサポートされる型のプロパティしかマップできませんでした。</span><span class="sxs-lookup"><span data-stu-id="afc9c-114">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="afc9c-115">値は、変換されずに列とプロパティの間で相互にコピーされていました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-115">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="afc9c-116">EF Core 2.1 以降では、列から取得された値を、値変換を適用して変換してから、プロパティに適用することができます。その逆も可能です。</span><span class="sxs-lookup"><span data-stu-id="afc9c-116">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="afc9c-117">必要に応じて、規則によって適用できる多くの変換に加え、列とプロパティ間のカスタム変換を登録できるようにする明示的な構成 API もあります。</span><span class="sxs-lookup"><span data-stu-id="afc9c-117">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="afc9c-118">この機能の用途をいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="afc9c-118">Some of the application of this feature are:</span></span>

- <span data-ttu-id="afc9c-119">列挙型を文字列として格納する</span><span class="sxs-lookup"><span data-stu-id="afc9c-119">Storing enums as strings</span></span>
- <span data-ttu-id="afc9c-120">符号なし整数と SQL Server とのマッピング</span><span class="sxs-lookup"><span data-stu-id="afc9c-120">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="afc9c-121">プロパティ値の自動での暗号化と復号化</span><span class="sxs-lookup"><span data-stu-id="afc9c-121">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="afc9c-122">このトピックの詳細については、[値変換のセクション](xref:core/modeling/value-conversions)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-122">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="afc9c-123">LINQ GroupBy 変換</span><span class="sxs-lookup"><span data-stu-id="afc9c-123">LINQ GroupBy translation</span></span>
<span data-ttu-id="afc9c-124">EF Core 2.1 より前のバージョンでは、GroupBy LINQ 演算子は常にメモリ内で評価されていました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-124">Before version 2.1, in EF Core the GroupBy LINQ operator was always be evaluated in memory.</span></span> <span data-ttu-id="afc9c-125">これを最も一般的なケースの SQL GROUP BY 句に変換することがサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-125">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="afc9c-126">この例では、さまざまな集計関数の計算に使用される GroupBy を使用したクエリを示しています。</span><span class="sxs-lookup"><span data-stu-id="afc9c-126">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => Amount)
        });
```

<span data-ttu-id="afc9c-127">対応する SQL 変換は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="afc9c-127">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="afc9c-128">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="afc9c-128">Data Seeding</span></span>
<span data-ttu-id="afc9c-129">新しいリリースでは、データベースに入力する初期データを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-129">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="afc9c-130">EF6 とは異なり、データのシード処理がモデルの構成の一部としてエンティティ型に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="afc9c-130">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="afc9c-131">EF Core の移行では、データベースをモデルの新しいバージョンにアップグレードするときに、挿入、更新、または削除のどの操作を適用する必要があるかを自動的に計算できます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-131">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="afc9c-132">次の例に示すように、これを使用して `OnModelCreating` の Post にシード データを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-132">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().SeedData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="afc9c-133">このトピックの詳細については、[データのシード処理に関するセクション](xref:core/modeling/data-seeding)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-133">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="afc9c-134">クエリ型</span><span class="sxs-lookup"><span data-stu-id="afc9c-134">Query types</span></span>
<span data-ttu-id="afc9c-135">EF Core モデルにクエリ型を含めることができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-135">An EF Core model can now include query types.</span></span> <span data-ttu-id="afc9c-136">エンティティ型とは異なり、クエリ型には定義されたキーがないため、クエリ型を挿入、削除または更新することができません (つまり、読み取り専用です)。ただし、クエリによって直接返すことができます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-136">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (i.e. they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="afc9c-137">クエリ型の使用シナリオをいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="afc9c-137">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="afc9c-138">主キーを使用しないビューへのマッピング</span><span class="sxs-lookup"><span data-stu-id="afc9c-138">Mapping to views without primary keys</span></span>
- <span data-ttu-id="afc9c-139">主キーを使用しないテーブルへのマッピング</span><span class="sxs-lookup"><span data-stu-id="afc9c-139">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="afc9c-140">モデルで定義されているクエリへのマッピング</span><span class="sxs-lookup"><span data-stu-id="afc9c-140">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="afc9c-141">`FromSql()` クエリの戻り値の型として機能</span><span class="sxs-lookup"><span data-stu-id="afc9c-141">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="afc9c-142">このトピックの詳細については、[クエリ型に関するセクション](xref:core/modeling/query-types)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-142">Read the [section on query types](xref:core/modeling/query-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="afc9c-143">派生型の Include</span><span class="sxs-lookup"><span data-stu-id="afc9c-143">Include for derived types</span></span>
<span data-ttu-id="afc9c-144">`Include` メソッドの式を記述する際に、派生型でのみ定義されているナビゲーション プロパティを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-144">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="afc9c-145">`Include` の厳密に型指定されたバージョンについては、明示的なキャストまたは `as` 演算子を使用してサポートします。</span><span class="sxs-lookup"><span data-stu-id="afc9c-145">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="afc9c-146">また、`Include` の文字列バージョンの派生型で定義されているナビゲーション プロパティの名前の参照もサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-146">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="afc9c-147">このトピックの詳細については、[派生型を使用した Include に関するセクション](xref:core/querying/related-data#include-on-derived-types)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-147">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="afc9c-148">System.Transactions のサポート</span><span class="sxs-lookup"><span data-stu-id="afc9c-148">System.Transactions support</span></span>
<span data-ttu-id="afc9c-149">TransactionScope などの System.Transactions 機能が使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-149">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="afc9c-150">これは、この機能をサポートするデータベース プロバイダーを使用する場合、.NET Framework と .NET Core の両方で機能します。</span><span class="sxs-lookup"><span data-stu-id="afc9c-150">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="afc9c-151">このトピックの詳細については、[System.Transactions に関するセクション](xref:core/saving/transactions#using-systemtransactions)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-151">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="afc9c-152">最初の移行での列の順序付けの改善</span><span class="sxs-lookup"><span data-stu-id="afc9c-152">Better column ordering in initial migration</span></span>
<span data-ttu-id="afc9c-153">お客様のフィードバックに基づき、移行で最初に生成されるテーブルの列を、クラスで宣言されているプロパティと同じ順序になるように更新しました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-153">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="afc9c-154">EF Core では、最初にテーブルが作成されると、その後で新しいメンバーを追加するときに順序を変更できないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-154">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="afc9c-155">相関サブクエリの最適化</span><span class="sxs-lookup"><span data-stu-id="afc9c-155">Optimization of correlated subqueries</span></span>
<span data-ttu-id="afc9c-156">プロジェクションでナビゲーション プロパティを使用して、ルート クエリからのデータと相関サブクエリからのデータが結合される多くの一般的なシナリオで、"N + 1" SQL クエリの実行を回避するようにクエリ変換を改善しました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-156">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="afc9c-157">最適化には、サブクエリからの結果のバッファリングが必要なため、新しい動作を受け入れるようにクエリを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="afc9c-157">The optimization requires buffering the results form the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="afc9c-158">例として、次のクエリは、通常、Customers (顧客) の 1 つのクエリに加え、Orders の N 個 ("N" は返された顧客の数) の個別のクエリに変換されます。</span><span class="sxs-lookup"><span data-stu-id="afc9c-158">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="afc9c-159">適切な場所に `ToList()` を含めることで、最適化を有効にするバッファリングが Orders に適していることを示します。</span><span class="sxs-lookup"><span data-stu-id="afc9c-159">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="afc9c-160">このクエリは、Customers と Orders の 2 つの SQL クエリにのみ変換されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-160">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

### <a name="ownedattribute"></a><span data-ttu-id="afc9c-161">OwnedAttribute</span><span class="sxs-lookup"><span data-stu-id="afc9c-161">OwnedAttribute</span></span>

<span data-ttu-id="afc9c-162">`[Owned]` で型に注釈を付けるだけで、[所有エンティティ型](xref:core/modeling/owned-entities)を構成して、所有者エンティティをモデルに確実に追加できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="afc9c-162">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="afc9c-163">データベース プロバイダーの互換性</span><span class="sxs-lookup"><span data-stu-id="afc9c-163">Database provider compatibility</span></span>

<span data-ttu-id="afc9c-164">EF Core 2.1 は、EF Core 2.0 用に作成されたデータベース プロバイダーと互換性があるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="afc9c-164">EF Core 2.1 was designed to be compatible with database providers created for EF Core 2.0.</span></span> <span data-ttu-id="afc9c-165">上記で説明した機能の一部 (値変換など) では更新されたプロバイダーが必要ですが、それ以外 (遅延読み込みなど) は既存のプロバイダーで機能します。</span><span class="sxs-lookup"><span data-stu-id="afc9c-165">While some of the features described above (e.g. value conversions) require an updated provider others (e.g. lazy loading) will light up with existing providers.</span></span>

> [!TIP]
> <span data-ttu-id="afc9c-166">予期しない非互換性や新機能で何らかの問題が見つかった場合、またはそれらに対するフィードバックがある場合は、[問題の追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues/new)を使用して報告してください。</span><span class="sxs-lookup"><span data-stu-id="afc9c-166">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
