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
# <a name="whats-new-in-ef-core-60"></a>EF Core 6.0 の新機能

EF Core 6.0 は現在開発中です。 ここには、各プレビューで導入された興味深い変更の概要が記載されています。

このページには [EF Core 6.0 のプラン](xref:core/what-is-new/ef-core-6.0/plan)を記載していません。 このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 6.0 のテーマ全体について説明します。

## <a name="ef-core-60-preview-1"></a>EF Core 6.0 Preview 1

> [!TIP]
> 以下に示す、すべての Preview 1 サンプルは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)して実行およびデバッグできます。

### <a name="unicodeattribute"></a>UnicodeAttribute

GitHub イシュー: [#19794](https://github.com/dotnet/efcore/issues/19794)。 この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。

EF Core 6.0 以降では、"_データベースの型を直接指定せずに_"、マッピング属性を使用して文字列プロパティを Unicode 以外の列にマップできるようになりました。 たとえば、[国際標準図書番号 (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) を表す "ISBN 978-3-16-148410-0" という形式のプロパティを持つ、`Book` エンティティ型について考えてみます。

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

ISBN に Unicode 以外の文字を含めることはできないため、`Unicode` 属性で Unicode 以外の文字列型が使用されるようになります。 また、データベース列のサイズを制限するために `MaxLength` が使用されます。 たとえば、SQL Server を使用している場合、`varchar(22)` というデータベース列になります。

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> 既定では、文字列プロパティが EF Core によって Unicode 列にマップされます。 データベース システムが Unicode 型のみをサポートしている場合、`UnicodeAttribute` は無視されます。

### <a name="precisionattribute"></a>PrecisionAttribute

GitHub イシュー: [#17914](https://github.com/dotnet/efcore/issues/17914)。 この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。

"_データベースの型を直接指定せずに_"、マッピング属性を使用してデータベース列の有効桁数と小数点以下桁数を構成できるようになりました。 たとえば、10 進数の `Price` プロパティを持つ `Product` エンティティ型について考えてみます。

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

このプロパティは、EF Core で有効桁数 10、小数点以下桁数 2 のデータベース列にマップされます。 たとえば、SQL Server の場合は次のようになります。

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a>EntityTypeConfigurationAttribute

GitHub イシュー: [#23163](https://github.com/dotnet/efcore/issues/23163)。 この機能には [@KaloyanIT](https://github.com/KaloyanIT) の貢献がありました。

<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> インスタンスで、各エンティティ型の <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 構成を独自の構成クラスに含めることができます。 例:

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

通常、この構成クラスは、インスタンスを作成したうえで <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> から呼び出す必要があります。 例:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

EF Core 6.0 以降では、エンティティ型に対して `EntityTypeConfigurationAttribute` を配置することで、EF Core で適切な構成を見つけて使用できます。 例:

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

この属性は、`Book` エンティティ型がモデルに含まれている場合、指定した `IEntityTypeConfiguration` の実装が EF Core で使用されることを意味します。 エンティティ型は、通常のいずれかのメカニズムを使用してモデルに含められます。 たとえば、エンティティ型に対して <xref:Microsoft.EntityFrameworkCore.DbSet%601> を使用します。

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

または、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> において登録します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> `EntityTypeConfigurationAttribute` 型は、アセンブリ内で自動的に検出されることはありません。 エンティティ型の属性が検出される前に、エンティティ型をモデルに追加する必要があります。

### <a name="translate-tostring-on-sqlite"></a>SQLite で ToString を変換する

GitHub イシュー: [#17223](https://github.com/dotnet/efcore/issues/17223)。 この機能には [@ralmsdeveloper](https://github.com/ralmsdeveloper) の貢献がありました。

SQLite データベース プロバイダー使用時、<xref:System.Object.ToString> の呼び出しが SQL に変換されるようになりました。 これは、文字列以外の列を含むテキスト検索に便利です。 たとえば、電話番号を数値として格納する `User` エンティティ型について考えてみます。

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

`ToString` を使用すると、データベース内の数値を文字列に変換できます。 次に、`LIKE` などの関数でこの文字列を使用して、パターンに一致する数値を検索できます。 たとえば、555 を含むすべての数値を検索するには、次のようにします。

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

これは、SQLite データベースを使用する場合、次の SQL に変換されます。

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

SQL Server 向けの <xref:System.Object.ToString> の変換は EF Core 5.0 で既にサポートされており、他のデータベース プロバイダーでもサポートされている可能性があることにご注意ください。

### <a name="effunctionsrandom"></a>EF.Functions.Random

GitHub イシュー: [#16141](https://github.com/dotnet/efcore/issues/16141)。 この機能には [@RaymondHuy](https://github.com/RaymondHuy) の貢献がありました。

`EF.Functions.Random` がデータベース関数にマップされ、0 と 1 の間 (それぞれを除く) の擬似乱数が返されます。 変換は、SQL Server、SQLite、Cosmos 用の EF Core リポジトリに実装されています。 たとえば、`Popularity` プロパティを持つ `User` エンティティ型について考えてみます。

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

`Popularity` は、1 から 5 までの値 (それぞれを含む) になります。 `EF.Functions.Random` を使用すると、ランダムに選択された評判を持つすべてのユーザーを返すクエリを作成できます。

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

これは、SQL Server データベースを使用する場合、次の SQL に変換されます。

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a>SQL Server スパース列のサポート

GitHub イシュー: [#8023](https://github.com/dotnet/efcore/issues/8023)。

SQL Server の[スパース列](/sql/relational-databases/tables/use-sparse-columns)は、null 値を格納するように最適化された通常の列です。 これは、[TPH 継承マッピング](xref:core/modeling/inheritance)を使用する場合に便利です。その場合、使用頻度の低いサブタイプのプロパティがテーブル内のほとんどの行で null 列値になります。 たとえば、`ForumUser` から拡張される `ForumModerator` クラスについて考えてみます。

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

数百万人のユーザーがいても、モデレーターはそのうちほんの一握りでしょう。 つまり、この場合は `ForumName` をスパースとしてマッピングすることに意味があります。 これを <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> で `IsSparse` を使用して構成できるようになりました。 例:

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

EF Core の移行で列がスパースとしてマークされます。 例:

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> スパース列には制限があります。 [SQL Server スパース列に関するドキュメント](/sql/relational-databases/tables/use-sparse-columns)を必ずお読みになり、スパース列がご自身のシナリオに確実に適した選択肢であるようにしてください。

### <a name="in-memory-database-validate-required-properties-are-not-null"></a>インメモリ データベース: 必須プロパティが null でないことを検証する

GitHub イシュー: [#10613](https://github.com/dotnet/efcore/issues/10613)。 この機能には [@fagnercarvalho](https://github.com/fagnercarvalho) の貢献がありました。

必須とマークされたプロパティに対して null 値を保存しようとした場合に、EF Core のインメモリ データベースで例外がスローされるようになりました。 たとえば、必須の `Username` プロパティを持つ `User` 型について考えてみます。

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

null の `Username` を持つエンティティを保存しようとすると、次の例外が発生します。

> Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'. (キー値 '{Id: 1}' のエンティティ型 'User' のインスタンスに必須プロパティ '{'Username'}' が欠落しています)

この検証は、必要に応じて無効にすることができます。 例:

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a>IsNullOrWhitespace の SQL Server 変換の向上

GitHub イシュー: [#22916](https://github.com/dotnet/efcore/issues/22916)。 この機能には [@Marusyk](https://github.com/Marusyk) の貢献がありました。

次のクエリがあるとします。

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

EF Core 6.0 より前は、これは SQL Server で次のように変換されていました。

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

この変換は、EF Core 6.0 で次のように改善されています。

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a>データベースのコメントはコード コメントにスキャフォールディングされる

GitHub イシュー: [#19113](https://github.com/dotnet/efcore/issues/19113)。 この機能には [@ErikEJ](https://github.com/ErikEJ) の貢献がありました。

SQL テーブルと列に対するコメントが、既存の SQL Server データベースから [EF Core モデルをリバース エンジニアリング](xref:core/managing-schemas/scaffolding)するときに作成されるエンティティ型にスキャフォールディングされるようになりました。 例:

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

## <a name="microsoftdatasqlite-60-preview-1"></a>Microsoft.Data.Sqlite 6.0 Preview 1

> [!TIP]
> 以下に示す、すべての Preview 1 サンプルは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6)して実行およびデバッグできます。

### <a name="savepoints-api"></a>セーブポイント API

GitHub イシュー: [#20228](https://github.com/dotnet/efcore/issues/20228)。

これまで [ADO.NET プロバイダーのセーブポイント用の共通 API](https://github.com/dotnet/runtime/issues/33397) を標準化してきました。 Microsoft.Data.Sqlite でこの API がサポートされるようになりました。これには以下が含まれます。

- トランザクション内にセーブポイントを作成する <xref:System.Data.Common.DbTransaction.Save(System.String)>
- 前のセーブポイントにロールバックする <xref:System.Data.Common.DbTransaction.Rollback(System.String)>
- セーブポイントを解放する <xref:System.Data.Common.DbTransaction.Release(System.String)>

セーブポイントを使用すると、トランザクション全体をロールバックすることなく、トランザクションの一部をロールバックすることができます。 たとえば、次のようなコードがあるとします。

- トランザクションを作成する
- データベースに更新を送信する
- セーブポイントを作成する
- 別の更新をデータベースに送信する
- 前に作成したセーブポイントにロールバックする
- トランザクションをコミットする

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

これにより、最初の更新がデータベースにコミットされる一方、トランザクションをコミットする前にセーブポイントがロールバックされたため、2 番目の更新はコミットされません。

### <a name="command-timeout-in-the-connection-string"></a>接続文字列でのコマンド タイムアウト

GitHub イシュー: [#22505](https://github.com/dotnet/efcore/issues/22505)。 この機能には [@nmichels](https://github.com/nmichels) の貢献がありました。

2 つの異なるタイムアウトが ADO.NET プロバイダーによってサポートされています。

- 接続タイムアウト。データベースへの接続を確立するときに待機する最大時間を決定します。
- コマンド タイムアウト。コマンドの実行が完了するまで待機する最大時間を決定します。

コマンド タイムアウトは、<xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> を使用してコードから設定できます。 このコマンド タイムアウトは、多くのプロバイダーによって接続文字列でも公開されています。 Microsoft.Data.Sqlite は、`Command Timeout` 接続文字列キーワードでこの傾向に従っています。 たとえば、`"Command Timeout=60;DataSource=test.db"` の場合、接続によって作成されるコマンドに対して既定のタイムアウトとして 60 秒が使用されます。

> [!TIP]
> Sqlite で `Default Timeout` は `Command Timeout` の同意語として扱われるため、必要に応じて代わりに使用できます。
