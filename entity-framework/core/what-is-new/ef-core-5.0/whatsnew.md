---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 304ed74fe344b43177525113c70b7be7bb0ac5ed
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238334"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="8700b-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="8700b-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="8700b-104">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="8700b-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="8700b-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="8700b-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="8700b-106">このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="8700b-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="8700b-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="8700b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="8700b-108">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-6"></a><span data-ttu-id="8700b-109">Preview 6</span><span class="sxs-lookup"><span data-stu-id="8700b-109">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="8700b-110">関連するコレクションのクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="8700b-110">Split queries for related collections</span></span>

<span data-ttu-id="8700b-111">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-111">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="8700b-112">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-112">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="8700b-113">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-113">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="8700b-114">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-114">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="8700b-115">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-115">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="8700b-116">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-116">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="8700b-117">インクルードを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="8700b-117">Split queries with Include</span></span>

<span data-ttu-id="8700b-118">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8700b-118">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="8700b-119">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-119">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="8700b-120">新しい `AsSplitQuery` API を使用して、この動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-120">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="8700b-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-121">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="8700b-122">AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-122">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="8700b-123">EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-123">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="8700b-124">クエリ ルートに対するすべての操作がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8700b-124">All operations on the query root are supported.</span></span> <span data-ttu-id="8700b-125">これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8700b-125">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="8700b-126">OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。</span><span class="sxs-lookup"><span data-stu-id="8700b-126">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="8700b-127">コレクション プロジェクションを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="8700b-127">Split queries with collection projections</span></span>

<span data-ttu-id="8700b-128">`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-128">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="8700b-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-129">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="8700b-130">この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-130">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="8700b-131">サポートされるのはコレクションの具体化のみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-131">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="8700b-132">上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。</span><span class="sxs-lookup"><span data-stu-id="8700b-132">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="8700b-133">この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-133">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="8700b-134">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="8700b-134">IndexAttribute</span></span>

<span data-ttu-id="8700b-135">新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-135">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="8700b-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-136">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="8700b-137">SQL Server の場合、移行によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-137">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="8700b-138">IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-138">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="8700b-139">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-139">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="8700b-140">SQL Server の場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-140">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="8700b-141">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-141">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="8700b-142">クエリ変換の例外の改善</span><span class="sxs-lookup"><span data-stu-id="8700b-142">Improved query translation exceptions</span></span>

<span data-ttu-id="8700b-143">クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。</span><span class="sxs-lookup"><span data-stu-id="8700b-143">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="8700b-144">たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8700b-144">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="8700b-145">EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8700b-145">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="8700b-146">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="8700b-146">Unhandled exception.</span></span> <span data-ttu-id="8700b-147">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.IsSigned)' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="8700b-147">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="8700b-148">追加情報:エンティティ型 'Artist' のメンバー 'IsSigned' の変換に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="8700b-148">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="8700b-149">指定されたメンバーがマップされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-149">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="8700b-150">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="8700b-150">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="8700b-151">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-151">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="8700b-152">同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-152">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="8700b-153">たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="8700b-153">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="8700b-154">EF Core では、次の例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-154">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="8700b-155">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="8700b-155">Unhandled exception.</span></span> <span data-ttu-id="8700b-156">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="8700b-156">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="8700b-157">追加情報:'StringComparison' 引数を受け取る 'string.Equals' メソッドの変換はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8700b-157">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="8700b-158">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2129535 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-158">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="8700b-159">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="8700b-159">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="8700b-160">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-160">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="8700b-161">トランザクション ID を指定する</span><span class="sxs-lookup"><span data-stu-id="8700b-161">Specify transaction ID</span></span>

<span data-ttu-id="8700b-162">この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-162">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="8700b-163">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="8700b-163">Many thanks for the contribution!</span></span>

<span data-ttu-id="8700b-164">EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-164">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="8700b-165">この ID は通常、トランザクションの開始時に EF Core によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-165">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="8700b-166">アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-166">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="8700b-167">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-167">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="8700b-168">IPAddress マッピング</span><span class="sxs-lookup"><span data-stu-id="8700b-168">IPAddress mapping</span></span>

<span data-ttu-id="8700b-169">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-169">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="8700b-170">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="8700b-170">Many thanks for the contribution!</span></span>

<span data-ttu-id="8700b-171">標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-171">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="8700b-172">たとえば、次のエンティティ型をマップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-172">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="8700b-173">SQL Server では、移行によって次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-173">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="8700b-174">これで、通常の方法でエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-174">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="8700b-175">結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-175">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="8700b-176">スキャフォールディング時の OnConfiguring を除外する</span><span class="sxs-lookup"><span data-stu-id="8700b-176">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="8700b-177">DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-177">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="8700b-178">ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="8700b-178">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="8700b-179">これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-179">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="8700b-180">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-180">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="8700b-181">または、パッケージ マネージャー コンソールで:</span><span class="sxs-lookup"><span data-stu-id="8700b-181">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="8700b-182">[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8700b-182">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="8700b-183">文字列の FirstOrDefault の変換</span><span class="sxs-lookup"><span data-stu-id="8700b-183">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="8700b-184">この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-184">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="8700b-185">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="8700b-185">Many thanks for the contribution!</span></span>

<span data-ttu-id="8700b-186">FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-186">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="8700b-187">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="8700b-187">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="8700b-188">は、SQL Server を使用すると、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-188">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="8700b-189">CASE ブロックの簡略化</span><span class="sxs-lookup"><span data-stu-id="8700b-189">Simplify case blocks</span></span>

<span data-ttu-id="8700b-190">EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-190">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="8700b-191">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="8700b-191">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="8700b-192">は、SQL Server では、正式には次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="8700b-192">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

<span data-ttu-id="8700b-193">しかし、今後は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-193">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="8700b-194">Preview 5</span><span class="sxs-lookup"><span data-stu-id="8700b-194">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="8700b-195">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="8700b-195">Database collations</span></span>

<span data-ttu-id="8700b-196">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-196">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="8700b-197">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="8700b-197">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="8700b-198">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-198">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="8700b-199">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-199">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="8700b-200">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-200">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="8700b-201">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-201">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="8700b-202">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-202">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="8700b-203">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="8700b-203">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="8700b-204">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-204">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="8700b-205">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-205">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="8700b-206">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-206">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="8700b-207">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-207">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="8700b-208">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="8700b-208">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="8700b-209">引数は、コマンド ラインから [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-209">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="8700b-210">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-210">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="8700b-211">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-211">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="8700b-212">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-212">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="8700b-213">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-213">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="8700b-214">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="8700b-214">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="8700b-215">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-215">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="8700b-216">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-216">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="8700b-217">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-217">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="8700b-218">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="8700b-218">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="8700b-219">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-219">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="8700b-220">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-220">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="8700b-221">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-221">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="8700b-222">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="8700b-222">Stored (persisted) computed columns</span></span>

<span data-ttu-id="8700b-223">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-223">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="8700b-224">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="8700b-224">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="8700b-225">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-225">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="8700b-226">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-226">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="8700b-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-227">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="8700b-228">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="8700b-228">SQLite computed columns</span></span>

<span data-ttu-id="8700b-229">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-229">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="8700b-230">Preview 4</span><span class="sxs-lookup"><span data-stu-id="8700b-230">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="8700b-231">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="8700b-231">Configure database precision/scale in model</span></span>

<span data-ttu-id="8700b-232">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-232">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="8700b-233">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-233">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="8700b-234">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-234">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="8700b-235">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-235">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="8700b-236">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="8700b-236">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="8700b-237">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-237">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="8700b-238">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-238">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="8700b-239">Preview 3</span><span class="sxs-lookup"><span data-stu-id="8700b-239">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="8700b-240">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="8700b-240">Filtered Include</span></span>

<span data-ttu-id="8700b-241">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-241">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="8700b-242">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-242">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="8700b-243">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-243">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="8700b-244">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-244">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="8700b-245">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-245">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="8700b-246">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-246">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="8700b-247">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-247">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="8700b-248">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="8700b-248">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="8700b-249">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-249">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="8700b-250">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-250">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="8700b-251">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="8700b-251">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="8700b-252">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-252">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="8700b-253">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-253">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="8700b-254">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-254">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="8700b-255">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="8700b-255">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="8700b-256">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-256">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="8700b-257">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="8700b-257">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="8700b-258">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-258">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="8700b-259">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-259">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="8700b-260">詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8700b-260">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="8700b-261">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="8700b-261">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="8700b-262">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="8700b-262">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="8700b-263">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="8700b-263">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="8700b-264">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8700b-264">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="8700b-265">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-265">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="8700b-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-266">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="8700b-267">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-267">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="8700b-268">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="8700b-268">Cosmos partition keys</span></span>

<span data-ttu-id="8700b-269">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-269">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="8700b-270">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-270">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="8700b-271">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-271">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="8700b-272">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="8700b-272">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="8700b-273">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-273">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="8700b-274">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-274">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="8700b-275">Preview 2</span><span class="sxs-lookup"><span data-stu-id="8700b-275">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="8700b-276">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="8700b-276">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="8700b-277">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-277">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="8700b-278">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="8700b-278">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="8700b-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-279">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="8700b-280">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-280">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="8700b-281">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="8700b-281">Complete discriminator mapping</span></span>

<span data-ttu-id="8700b-282">EF Core では、[継承階層の TPH マッピング](/ef/core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-282">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="8700b-283">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-283">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="8700b-284">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-284">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="8700b-285">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-285">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="8700b-286">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-286">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="8700b-287">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="8700b-287">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="8700b-288">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="8700b-288">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="8700b-289">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-289">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="8700b-290">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-290">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="8700b-291">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-291">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="8700b-292">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-292">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="8700b-293">Preview 1</span><span class="sxs-lookup"><span data-stu-id="8700b-293">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="8700b-294">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="8700b-294">Simple logging</span></span>

<span data-ttu-id="8700b-295">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-295">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="8700b-296">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-296">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="8700b-297">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8700b-297">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="8700b-298">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-298">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="8700b-299">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="8700b-299">Simple way to get generated SQL</span></span>

<span data-ttu-id="8700b-300">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="8700b-300">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="8700b-301">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8700b-301">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="8700b-302">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-302">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="8700b-303">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="8700b-303">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="8700b-304">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-304">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="8700b-305">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-305">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="8700b-306">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-306">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="8700b-307">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="8700b-307">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="8700b-308">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-308">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="8700b-309">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-309">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="8700b-310">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8700b-310">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="8700b-311">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-311">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="8700b-312">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="8700b-312">Change-tracking proxies</span></span>

<span data-ttu-id="8700b-313">EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-313">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="8700b-314">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="8700b-314">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="8700b-315">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="8700b-315">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="8700b-316">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-316">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="8700b-317">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="8700b-317">Enhanced debug views</span></span>

<span data-ttu-id="8700b-318">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-318">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="8700b-319">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-319">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="8700b-320">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="8700b-320">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="8700b-321">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8700b-321">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="8700b-322">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-322">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="8700b-323">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="8700b-323">Improved handling of database null semantics</span></span>

<span data-ttu-id="8700b-324">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="8700b-324">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="8700b-325">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="8700b-325">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="8700b-326">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="8700b-326">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="8700b-327">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="8700b-327">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="8700b-328">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-328">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="8700b-329">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="8700b-329">Indexer properties</span></span>

<span data-ttu-id="8700b-330">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8700b-330">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="8700b-331">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8700b-331">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="8700b-332">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-332">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="8700b-333">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="8700b-333">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="8700b-334">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-334">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="8700b-335">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-335">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="8700b-336">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-336">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="8700b-337">IsRelational</span><span class="sxs-lookup"><span data-stu-id="8700b-337">IsRelational</span></span>

<span data-ttu-id="8700b-338">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="8700b-338">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="8700b-339">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="8700b-339">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="8700b-340">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-340">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="8700b-341">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-341">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="8700b-342">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="8700b-342">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="8700b-343">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-343">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="8700b-344">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="8700b-344">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="8700b-345">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](https://docs.microsoft.com/ef/core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="8700b-345">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="8700b-346">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-346">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="8700b-347">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="8700b-347">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="8700b-348">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-348">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="8700b-349">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="8700b-349">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="8700b-350">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="8700b-350">DateDiffWeek</span></span>
* <span data-ttu-id="8700b-351">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="8700b-351">DateFromParts</span></span>

<span data-ttu-id="8700b-352">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-352">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="8700b-353">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-353">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="8700b-354">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="8700b-354">Query translations for more byte array constructs</span></span>

<span data-ttu-id="8700b-355">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-355">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="8700b-356">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8700b-356">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="8700b-357">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-357">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="8700b-358">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="8700b-358">Query translation for Reverse</span></span>

<span data-ttu-id="8700b-359">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-359">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="8700b-360">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8700b-360">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="8700b-361">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-361">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="8700b-362">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="8700b-362">Query translation for bitwise operators</span></span>

<span data-ttu-id="8700b-363">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-363">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="8700b-364">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-364">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="8700b-365">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="8700b-365">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="8700b-366">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8700b-366">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="8700b-367">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="8700b-367">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
