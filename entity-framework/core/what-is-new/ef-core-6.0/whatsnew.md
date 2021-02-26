---
title: EF Core 6.0 の新機能
description: EF Core 6.0 の新機能の概要
author: ajcvickers
ms.date: 01/28/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: bcc2b3ce9047a2c6b5a89e99b96919914bcf42fe
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543199"
---
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="e274a-103">EF Core 6.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="e274a-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="e274a-104">EF Core 6.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="e274a-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="e274a-105">ここには、各プレビューで導入された興味深い変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="e274a-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="e274a-106">このページには [EF Core 6.0 のプラン](xref:core/what-is-new/ef-core-6.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="e274a-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="e274a-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 6.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="e274a-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="e274a-108">EF Core 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="e274a-108">EF Core 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="e274a-109">以下に示す、すべての Preview 1 サンプルは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)して実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="e274a-109">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="e274a-110">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="e274a-110">UnicodeAttribute</span></span>

<span data-ttu-id="e274a-111">GitHub イシュー: [#19794](https://github.com/dotnet/efcore/issues/19794)。</span><span class="sxs-lookup"><span data-stu-id="e274a-111">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="e274a-112">この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-112">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="e274a-113">EF Core 6.0 以降では、"_データベースの型を直接指定せずに_"、マッピング属性を使用して文字列プロパティを Unicode 以外の列にマップできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-113">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="e274a-114">たとえば、[国際標準図書番号 (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) を表す "ISBN 978-3-16-148410-0" という形式のプロパティを持つ、`Book` エンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-114">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

<span data-ttu-id="e274a-115">ISBN に Unicode 以外の文字を含めることはできないため、`Unicode` 属性で Unicode 以外の文字列型が使用されるようになります。</span><span class="sxs-lookup"><span data-stu-id="e274a-115">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="e274a-116">また、データベース列のサイズを制限するために `MaxLength` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-116">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="e274a-117">たとえば、SQL Server を使用している場合、`varchar(22)` というデータベース列になります。</span><span class="sxs-lookup"><span data-stu-id="e274a-117">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="e274a-118">既定では、文字列プロパティが EF Core によって Unicode 列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-118">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="e274a-119">データベース システムが Unicode 型のみをサポートしている場合、`UnicodeAttribute` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-119">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="e274a-120">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="e274a-120">PrecisionAttribute</span></span>

<span data-ttu-id="e274a-121">GitHub イシュー: [#17914](https://github.com/dotnet/efcore/issues/17914)。</span><span class="sxs-lookup"><span data-stu-id="e274a-121">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="e274a-122">この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-122">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="e274a-123">"_データベースの型を直接指定せずに_"、マッピング属性を使用してデータベース列の有効桁数と小数点以下桁数を構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-123">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="e274a-124">たとえば、10 進数の `Price` プロパティを持つ `Product` エンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-124">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="e274a-125">このプロパティは、EF Core で有効桁数 10、小数点以下桁数 2 のデータベース列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-125">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="e274a-126">たとえば、SQL Server の場合は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e274a-126">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="e274a-127">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="e274a-127">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="e274a-128">GitHub イシュー: [#23163](https://github.com/dotnet/efcore/issues/23163)。</span><span class="sxs-lookup"><span data-stu-id="e274a-128">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="e274a-129">この機能には [@KaloyanIT](https://github.com/KaloyanIT) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-129">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="e274a-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> インスタンスで、各エンティティ型の <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 構成を独自の構成クラスに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e274a-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="e274a-131">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-131">For example:</span></span>

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

<span data-ttu-id="e274a-132">通常、この構成クラスは、インスタンスを作成したうえで <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> から呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-132">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e274a-133">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-133">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="e274a-134">EF Core 6.0 以降では、エンティティ型に対して `EntityTypeConfigurationAttribute` を配置することで、EF Core で適切な構成を見つけて使用できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-134">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="e274a-135">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-135">For example:</span></span>

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

<span data-ttu-id="e274a-136">この属性は、`Book` エンティティ型がモデルに含まれている場合、指定した `IEntityTypeConfiguration` の実装が EF Core で使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e274a-136">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="e274a-137">エンティティ型は、通常のいずれかのメカニズムを使用してモデルに含められます。</span><span class="sxs-lookup"><span data-stu-id="e274a-137">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="e274a-138">たとえば、エンティティ型に対して <xref:Microsoft.EntityFrameworkCore.DbSet%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="e274a-138">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="e274a-139">または、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> において登録します。</span><span class="sxs-lookup"><span data-stu-id="e274a-139">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="e274a-140">`EntityTypeConfigurationAttribute` 型は、アセンブリ内で自動的に検出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="e274a-140">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="e274a-141">エンティティ型の属性が検出される前に、エンティティ型をモデルに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-141">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="e274a-142">SQLite で ToString を変換する</span><span class="sxs-lookup"><span data-stu-id="e274a-142">Translate ToString on SQLite</span></span>

<span data-ttu-id="e274a-143">GitHub イシュー: [#17223](https://github.com/dotnet/efcore/issues/17223)。</span><span class="sxs-lookup"><span data-stu-id="e274a-143">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="e274a-144">この機能には [@ralmsdeveloper](https://github.com/ralmsdeveloper) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-144">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="e274a-145">SQLite データベース プロバイダー使用時、<xref:System.Object.ToString> の呼び出しが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-145">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="e274a-146">これは、文字列以外の列を含むテキスト検索に便利です。</span><span class="sxs-lookup"><span data-stu-id="e274a-146">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="e274a-147">たとえば、電話番号を数値として格納する `User` エンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-147">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="e274a-148">`ToString` を使用すると、データベース内の数値を文字列に変換できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-148">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="e274a-149">次に、`LIKE` などの関数でこの文字列を使用して、パターンに一致する数値を検索できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-149">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="e274a-150">たとえば、555 を含むすべての数値を検索するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e274a-150">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="e274a-151">これは、SQLite データベースを使用する場合、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-151">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="e274a-152">SQL Server 向けの <xref:System.Object.ToString> の変換は EF Core 5.0 で既にサポートされており、他のデータベース プロバイダーでもサポートされている可能性があることにご注意ください。</span><span class="sxs-lookup"><span data-stu-id="e274a-152">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="e274a-153">EF.Functions.Random</span><span class="sxs-lookup"><span data-stu-id="e274a-153">EF.Functions.Random</span></span>

<span data-ttu-id="e274a-154">GitHub イシュー: [#16141](https://github.com/dotnet/efcore/issues/16141)。</span><span class="sxs-lookup"><span data-stu-id="e274a-154">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="e274a-155">この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-155">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="e274a-156">`EF.Functions.Random` がデータベース関数にマップされ、0 と 1 の間 (それぞれを除く) の擬似乱数が返されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-156">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="e274a-157">変換は、SQL Server、SQLite、Cosmos 用の EF Core リポジトリに実装されています。</span><span class="sxs-lookup"><span data-stu-id="e274a-157">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="e274a-158">たとえば、`Popularity` プロパティを持つ `User` エンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-158">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="e274a-159">`Popularity` は、1 から 5 までの値 (それぞれを含む) になります。</span><span class="sxs-lookup"><span data-stu-id="e274a-159">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="e274a-160">`EF.Functions.Random` を使用すると、ランダムに選択された評判を持つすべてのユーザーを返すクエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-160">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="e274a-161">これは、SQL Server データベースを使用する場合、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-161">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="e274a-162">SQL Server スパース列のサポート</span><span class="sxs-lookup"><span data-stu-id="e274a-162">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="e274a-163">GitHub イシュー: [#8023](https://github.com/dotnet/efcore/issues/8023)。</span><span class="sxs-lookup"><span data-stu-id="e274a-163">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="e274a-164">SQL Server の[スパース列](/sql/relational-databases/tables/use-sparse-columns)は、null 値を格納するように最適化された通常の列です。</span><span class="sxs-lookup"><span data-stu-id="e274a-164">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="e274a-165">これは、[TPH 継承マッピング](xref:core/modeling/inheritance)を使用する場合に便利です。その場合、使用頻度の低いサブタイプのプロパティがテーブル内のほとんどの行で null 列値になります。</span><span class="sxs-lookup"><span data-stu-id="e274a-165">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="e274a-166">たとえば、`ForumUser` から拡張される `ForumModerator` クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-166">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

<span data-ttu-id="e274a-167">数百万人のユーザーがいても、モデレーターはそのうちほんの一握りでしょう。</span><span class="sxs-lookup"><span data-stu-id="e274a-167">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="e274a-168">つまり、この場合は `ForumName` をスパースとしてマッピングすることに意味があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-168">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="e274a-169">これを <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> で `IsSparse` を使用して構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-169">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="e274a-170">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-170">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

<span data-ttu-id="e274a-171">EF Core の移行で列がスパースとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-171">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="e274a-172">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-172">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="e274a-173">スパース列には制限があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-173">Sparse columns have limitations.</span></span> <span data-ttu-id="e274a-174">[SQL Server スパース列に関するドキュメント](/sql/relational-databases/tables/use-sparse-columns)を必ずお読みになり、スパース列がご自身のシナリオに確実に適した選択肢であるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="e274a-174">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="e274a-175">インメモリ データベース: 必須プロパティが null でないことを検証する</span><span class="sxs-lookup"><span data-stu-id="e274a-175">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="e274a-176">GitHub イシュー: [#10613](https://github.com/dotnet/efcore/issues/10613)。</span><span class="sxs-lookup"><span data-stu-id="e274a-176">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="e274a-177">この機能には [@fagnercarvalho](https://github.com/fagnercarvalho) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-177">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="e274a-178">必須とマークされたプロパティに対して null 値を保存しようとした場合に、EF Core のインメモリ データベースで例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-178">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="e274a-179">たとえば、必須の `Username` プロパティを持つ `User` 型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e274a-179">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="e274a-180">null の `Username` を持つエンティティを保存しようとすると、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="e274a-180">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="e274a-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'. (キー値 '{Id: 1}' のエンティティ型 'User' のインスタンスに必須プロパティ '{'Username'}' が欠落しています)</span><span class="sxs-lookup"><span data-stu-id="e274a-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="e274a-182">この検証は、必要に応じて無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e274a-182">This validation can be disabled if necessary.</span></span> <span data-ttu-id="e274a-183">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-183">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="e274a-184">IsNullOrWhitespace の SQL Server 変換の向上</span><span class="sxs-lookup"><span data-stu-id="e274a-184">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="e274a-185">GitHub イシュー: [#22916](https://github.com/dotnet/efcore/issues/22916)。</span><span class="sxs-lookup"><span data-stu-id="e274a-185">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="e274a-186">この機能には [@Marusyk](https://github.com/Marusyk) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-186">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="e274a-187">次のクエリがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e274a-187">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="e274a-188">EF Core 6.0 より前は、これは SQL Server で次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="e274a-188">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="e274a-189">この変換は、EF Core 6.0 で次のように改善されています。</span><span class="sxs-lookup"><span data-stu-id="e274a-189">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="e274a-190">データベースのコメントはコード コメントにスキャフォールディングされる</span><span class="sxs-lookup"><span data-stu-id="e274a-190">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="e274a-191">GitHub イシュー: [#19113](https://github.com/dotnet/efcore/issues/19113)。</span><span class="sxs-lookup"><span data-stu-id="e274a-191">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="e274a-192">この機能には [@ErikEJ](https://github.com/ErikEJ) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-192">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="e274a-193">SQL テーブルと列に対するコメントが、既存の SQL Server データベースから [EF Core モデルをリバース エンジニアリング](xref:core/managing-schemas/scaffolding)するときに作成されるエンティティ型にスキャフォールディングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e274a-193">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="e274a-194">例:</span><span class="sxs-lookup"><span data-stu-id="e274a-194">For example:</span></span>

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="e274a-195">Microsoft.Data.Sqlite 6.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="e274a-195">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="e274a-196">以下に示す、すべての Preview 1 サンプルは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)して実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="e274a-196">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="e274a-197">セーブポイント API</span><span class="sxs-lookup"><span data-stu-id="e274a-197">Savepoints API</span></span>

<span data-ttu-id="e274a-198">GitHub イシュー: [#20228](https://github.com/dotnet/efcore/issues/20228)。</span><span class="sxs-lookup"><span data-stu-id="e274a-198">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="e274a-199">これまで [ADO.NET プロバイダーのセーブポイント用の共通 API](https://github.com/dotnet/runtime/issues/33397) を標準化してきました。</span><span class="sxs-lookup"><span data-stu-id="e274a-199">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="e274a-200">Microsoft.Data.Sqlite でこの API がサポートされるようになりました。これには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e274a-200">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="e274a-201">トランザクション内にセーブポイントを作成する <xref:System.Data.Common.DbTransaction.Save(System.String)></span><span class="sxs-lookup"><span data-stu-id="e274a-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="e274a-202">前のセーブポイントにロールバックする <xref:System.Data.Common.DbTransaction.Rollback(System.String)></span><span class="sxs-lookup"><span data-stu-id="e274a-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="e274a-203">セーブポイントを解放する <xref:System.Data.Common.DbTransaction.Release(System.String)></span><span class="sxs-lookup"><span data-stu-id="e274a-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="e274a-204">セーブポイントを使用すると、トランザクション全体をロールバックすることなく、トランザクションの一部をロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="e274a-204">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="e274a-205">たとえば、次のようなコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e274a-205">For example, the code below:</span></span>

- <span data-ttu-id="e274a-206">トランザクションを作成する</span><span class="sxs-lookup"><span data-stu-id="e274a-206">Creates a transaction</span></span>
- <span data-ttu-id="e274a-207">データベースに更新を送信する</span><span class="sxs-lookup"><span data-stu-id="e274a-207">Sends an update to the database</span></span>
- <span data-ttu-id="e274a-208">セーブポイントを作成する</span><span class="sxs-lookup"><span data-stu-id="e274a-208">Creates a savepoint</span></span>
- <span data-ttu-id="e274a-209">別の更新をデータベースに送信する</span><span class="sxs-lookup"><span data-stu-id="e274a-209">Sends another update to the database</span></span>
- <span data-ttu-id="e274a-210">前に作成したセーブポイントにロールバックする</span><span class="sxs-lookup"><span data-stu-id="e274a-210">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="e274a-211">トランザクションをコミットする</span><span class="sxs-lookup"><span data-stu-id="e274a-211">Commits the transaction</span></span>

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

<span data-ttu-id="e274a-212">これにより、最初の更新がデータベースにコミットされる一方、トランザクションをコミットする前にセーブポイントがロールバックされたため、2 番目の更新はコミットされません。</span><span class="sxs-lookup"><span data-stu-id="e274a-212">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="e274a-213">接続文字列でのコマンド タイムアウト</span><span class="sxs-lookup"><span data-stu-id="e274a-213">Command timeout in the connection string</span></span>

<span data-ttu-id="e274a-214">GitHub イシュー: [#22505](https://github.com/dotnet/efcore/issues/22505)。</span><span class="sxs-lookup"><span data-stu-id="e274a-214">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="e274a-215">この機能には [@nmichels](https://github.com/nmichels) の貢献がありました。</span><span class="sxs-lookup"><span data-stu-id="e274a-215">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="e274a-216">2 つの異なるタイムアウトが ADO.NET プロバイダーによってサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e274a-216">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="e274a-217">接続タイムアウト。データベースへの接続を確立するときに待機する最大時間を決定します。</span><span class="sxs-lookup"><span data-stu-id="e274a-217">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="e274a-218">コマンド タイムアウト。コマンドの実行が完了するまで待機する最大時間を決定します。</span><span class="sxs-lookup"><span data-stu-id="e274a-218">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="e274a-219">コマンド タイムアウトは、<xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> を使用してコードから設定できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-219">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e274a-220">このコマンド タイムアウトは、多くのプロバイダーによって接続文字列でも公開されています。</span><span class="sxs-lookup"><span data-stu-id="e274a-220">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="e274a-221">Microsoft.Data.Sqlite は、`Command Timeout` 接続文字列キーワードでこの傾向に従っています。</span><span class="sxs-lookup"><span data-stu-id="e274a-221">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="e274a-222">たとえば、`"Command Timeout=60;DataSource=test.db"` の場合、接続によって作成されるコマンドに対して既定のタイムアウトとして 60 秒が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e274a-222">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="e274a-223">Sqlite で `Default Timeout` は `Command Timeout` の同意語として扱われるため、必要に応じて代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-223">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
