---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: f822e3ae776778749a654377cbd9d9814ca40972
ms.sourcegitcommit: 12d257db4786487a0c28e9ddd79f176f7cf6edb1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89043598"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="85fd2-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="85fd2-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="85fd2-104">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="85fd2-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="85fd2-106">このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="85fd2-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="85fd2-108">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="85fd2-109">Preview 8</span><span class="sxs-lookup"><span data-stu-id="85fd2-109">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="85fd2-110">Table-Per-Type (TPT) のマッピング</span><span class="sxs-lookup"><span data-stu-id="85fd2-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="85fd2-111">EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="85fd2-112">これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="85fd2-113">EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="85fd2-114">たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="85fd2-114">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="85fd2-115">EF Core の既定では、これが 1 つのテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-115">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="85fd2-116">一方、各エンティティ型を別のテーブルにマップすると、代わりに型ごとに 1 つのテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="85fd2-117">前述の外部キー制約の作成は、プレビュー 8 のコードを分岐した後に追加されたことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="85fd2-118">エンティティ型を別のテーブルにマップするには、マッピング属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="85fd2-119">または `ModelBuilder` 構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="85fd2-120">ドキュメントは、イシュー [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="85fd2-121">移行:SQLite テーブルを再構築する</span><span class="sxs-lookup"><span data-stu-id="85fd2-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="85fd2-122">他のデータベースと比較すると、SQLite のスキーマ操作機能は比較的限られています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="85fd2-123">たとえば、既存のテーブルから列を削除するには、テーブル全体を削除して再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="85fd2-124">EF Core 5.0 の移行では、必要なスキーマ変更のためにテーブルの自動再構築がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="85fd2-125">たとえば、エンティティ型 `Unicorn` に対して作成された `Unicorns` テーブルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="85fd2-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="85fd2-126">次に、ユニコーンの年齢を格納することは非常に失礼だと考えられることがわかったので、そのプロパティを削除し、新しい移行を追加して、データベースを更新しましょう。</span><span class="sxs-lookup"><span data-stu-id="85fd2-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="85fd2-127">EF Core 3.1 を使用している場合は列を削除できないため、この更新は失敗します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="85fd2-128">EF Core 5.0 ではそうではなく、移行によってテーブルが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="85fd2-129">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-129">Notice that:</span></span>
* <span data-ttu-id="85fd2-130">新しいテーブルに必要なスキーマを使用して一時テーブルが作成されます</span><span class="sxs-lookup"><span data-stu-id="85fd2-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="85fd2-131">データは現在のテーブルから一時テーブルにコピーされます</span><span class="sxs-lookup"><span data-stu-id="85fd2-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="85fd2-132">外部キーの適用はオフに切り替えられます</span><span class="sxs-lookup"><span data-stu-id="85fd2-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="85fd2-133">現在のテーブルはドロップされます</span><span class="sxs-lookup"><span data-stu-id="85fd2-133">The current table is dropped</span></span>
* <span data-ttu-id="85fd2-134">一時テーブルは新しいテーブルとして名前が変更されます</span><span class="sxs-lookup"><span data-stu-id="85fd2-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="85fd2-135">ドキュメントは、イシュー [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="85fd2-136">テーブル値関数</span><span class="sxs-lookup"><span data-stu-id="85fd2-136">Table-valued functions</span></span>

<span data-ttu-id="85fd2-137">この機能は、[@pmiddleton](https://github.com/pmiddleton) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="85fd2-138">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="85fd2-139">EF Core 5.0 には、.NET メソッドをテーブル値関数 (TVF) にマップするための最上級のサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="85fd2-140">これらの関数を LINQ クエリで使用することができます。また、この関数の結果に対する追加の構成も SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="85fd2-141">たとえば、SQL Server データベースで定義されている次の TVF について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="85fd2-141">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="85fd2-142">EF Core モデルでは、この TVF を使用するために 2 つのエンティティ型が必要です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="85fd2-143">通常の方法で Employees テーブルにマップする `Employee` 型</span><span class="sxs-lookup"><span data-stu-id="85fd2-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="85fd2-144">TVF から返されるシェイプと一致する `Report` 型</span><span class="sxs-lookup"><span data-stu-id="85fd2-144">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="85fd2-145">これらの型は、EF Core モデルに含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="85fd2-146">`Report` には主キーがないため、そのように構成する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="85fd2-147">最後に、.NET メソッドをデータベースの TVF にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="85fd2-148">このメソッドは、新しい `FromExpression` メソッドを使用して DbContext に対して定義できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="85fd2-149">このメソッドには、上記で定義された TVF に一致するパラメーターと戻り値の型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="85fd2-150">次に、メソッドは OnModelCreating の EF Core モデルに追加されます</span><span class="sxs-lookup"><span data-stu-id="85fd2-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="85fd2-151">(ここでラムダを使用すると、`MethodInfo` を EF Core に簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="85fd2-152">メソッドに渡された引数は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="85fd2-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="85fd2-153">これで、`GetReports` を呼び出して結果を構成するクエリを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="85fd2-154">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-154">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="85fd2-155">SQL Server では、これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="85fd2-156">SQL では `Employees` テーブルがルートとされ、`GetReports` が呼び出され、関数の結果に WHERE 句が追加されることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="85fd2-157">柔軟なクエリと更新マップ</span><span class="sxs-lookup"><span data-stu-id="85fd2-157">Flexible query/update mapping</span></span>

<span data-ttu-id="85fd2-158">EF Core 5.0 では、同じエンティティ型を異なるデータベース オブジェクトにマップできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="85fd2-159">これらのオブジェクトは、テーブル、ビュー、または関数です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="85fd2-160">たとえば、エンティティ型はデータベース ビューとデータベース テーブルの両方にマップできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="85fd2-161">既定では、EF Core はビューからクエリを実行し、更新をテーブルに送信します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="85fd2-162">たとえば、次のコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="85fd2-163">ビューに対してクエリを実行し、テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="85fd2-164">コンテキスト全体の分割クエリ構成</span><span class="sxs-lookup"><span data-stu-id="85fd2-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="85fd2-165">分割クエリ (以下を参照してください) は、DbContext によって実行されるクエリの既定値として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="85fd2-166">この構成はリレーショナル プロバイダーの場合にのみ使用できるため、`UseProvider` 構成の一部として指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="85fd2-167">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="85fd2-168">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="85fd2-169">PhysicalAddress のマッピング</span><span class="sxs-lookup"><span data-stu-id="85fd2-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="85fd2-170">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="85fd2-171">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="85fd2-172">この構成はリレーショナル プロバイダーでのみ使用できるた標準の .NET [PhysicalAddress クラス](/dotnet/api/system.net.networkinformation.physicaladdress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="85fd2-173">詳細については、以下の `IPAddress` の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="85fd2-174">Preview 7</span><span class="sxs-lookup"><span data-stu-id="85fd2-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="85fd2-175">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="85fd2-175">DbContextFactory</span></span>

<span data-ttu-id="85fd2-176">EF Core 5.0 には、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="85fd2-177">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="85fd2-178">これにより、ASP.NET Core コントローラーなどのアプリケーション サービスが、サービス コンストラクターの `IDbContextFactory<TContext>` に依存できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="85fd2-179">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-179">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="85fd2-180">これにより、DbContext インスタンスを必要に応じて作成および使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="85fd2-181">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="85fd2-182">なお、この方法で作成された DbContext インスタンスは、アプリケーションのサービス プロバイダーには管理 "_されず_"、従ってアプリケーションによって破棄される必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="85fd2-183">この分離は、`IDbContextFactory` の使用が推奨される Blazor アプリケーションで非常に便利ですが、他のシナリオでも役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="85fd2-184">DbContext インスタンスは、`AddPooledDbContextFactory` を呼び出すことによってプールできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="85fd2-185">このプールは、`AddDbContextPool` の場合と同じように動作しますが、同じ制限もあります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="85fd2-186">ドキュメントは、イシュー [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="85fd2-187">DbContext の状態のリセット</span><span class="sxs-lookup"><span data-stu-id="85fd2-187">Reset DbContext state</span></span>

<span data-ttu-id="85fd2-188">EF Core 5.0 には、追跡対象のすべてのエンティティの DbContext をクリアする `ChangeTracker.Clear()` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="85fd2-189">これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="85fd2-190">ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、パフォーマンスと堅牢性が向上します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="85fd2-191">ドキュメントは、イシュー [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="85fd2-192">ストアで生成された既定値に新しいパターン</span><span class="sxs-lookup"><span data-stu-id="85fd2-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="85fd2-193">EF Core では、既定の制約値がある列に、値を明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="85fd2-194">EF Core では、このセンチネルとして、プロパティ型に CLR の既定値を使用しています。つまり、その値が CLR の既定でない場合はそれが挿入され、それ以外の場合は、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="85fd2-195">これは、既定の CLR が適切なセンチネルではない型 (特に、`bool` のプロパティ) で問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="85fd2-196">EF Core 5.0 で、次のようなケースで、バッキング フィールドに null 値が許容されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="85fd2-197">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-197">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="85fd2-198">なお、null 値はバッキング フィールドでは許容されますが、公開されているプロパティでは許容されません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="85fd2-199">これにより、エンティティ型の公開サーフェスに影響を与えることなく、センチネル値を `null` にできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="85fd2-200">この場合、`IsValid` が設定されない場合、バッキング フィールドは null のままであるため、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="85fd2-201">`true` または `false` のいずれかが設定される場合、この値はデータベースに明示的に保存されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="85fd2-202">ドキュメントは、イシュー [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="85fd2-203">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="85fd2-203">Cosmos partition keys</span></span>

<span data-ttu-id="85fd2-204">EF Core では、EF モデルに Cosmos パーティション キーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="85fd2-205">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="85fd2-206">Preview 7 以降では、一部のクエリのパフォーマンスの向上のために、パーティション キーがエンティティ型の PK に含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="85fd2-207">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="85fd2-208">Cosmos の構成</span><span class="sxs-lookup"><span data-stu-id="85fd2-208">Cosmos configuration</span></span>

<span data-ttu-id="85fd2-209">EF Core 5.0 では、Cosmos と Cosmos への接続の構成が改善されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="85fd2-210">以前は、EF Core を Cosmos データベースに接続する場合に、エンドポイントとキーを明示的に指定する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="85fd2-211">EF Core 5.0 では、代わりに接続文字列を使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="85fd2-212">また、EF Core 5.0 では、WebProxy インスタンスを明示的に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="85fd2-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="85fd2-214">その他、多くのタイムアウト値、制限なども構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="85fd2-215">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-215">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="85fd2-216">最後に、一般により互換性がある、`ConnectionMode.Gateway` が既定の接続モードになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="85fd2-217">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="85fd2-218">DbContext のスキャフォールディングの単数化</span><span class="sxs-lookup"><span data-stu-id="85fd2-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="85fd2-219">以前は EF Core で、既存のデータベースから DbContext をスキャフォールディングする場合、データベース内のテーブル名と一致するエンティティ型名が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="85fd2-220">たとえば、`People` テーブルと `Addresses` テーブルには、`People` と `Addresses` という名前のエンティティ型が生成されていました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="85fd2-221">この動作は、以前のリリースでは、複数形化サービスの登録によって構成できました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="85fd2-222">EF Core 5.0 では、既定の複数形化サービスとして [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) パッケージが使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="85fd2-223">つまり、`People` テーブルと `Addresses` テーブルは、`Person` と `Address` という名前のエンティティ型にリバース エンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="85fd2-224">セーブポイント</span><span class="sxs-lookup"><span data-stu-id="85fd2-224">Savepoints</span></span>

<span data-ttu-id="85fd2-225">EF Core では、複数の操作を実行するトランザクションをより細かく制御する、[セーブポイント](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-225">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="85fd2-226">セーブポイントは、手動で作成、解放、ロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="85fd2-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="85fd2-228">また、`SaveChanges` の実行に失敗すると、EF Core では最後のセーブポイントにロールバックされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="85fd2-229">これにより、トランザクション全体を再実行しなくても、SaveChanges を再実行できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="85fd2-230">ドキュメントは、イシュー [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="85fd2-231">Preview 6</span><span class="sxs-lookup"><span data-stu-id="85fd2-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="85fd2-232">関連するコレクションのクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="85fd2-232">Split queries for related collections</span></span>

<span data-ttu-id="85fd2-233">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="85fd2-234">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="85fd2-235">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="85fd2-236">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="85fd2-237">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="85fd2-238">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="85fd2-239">インクルードを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="85fd2-239">Split queries with Include</span></span>

<span data-ttu-id="85fd2-240">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="85fd2-241">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="85fd2-242">新しい `AsSplitQuery` API を使用して、この動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="85fd2-243">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="85fd2-244">AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="85fd2-245">EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-245">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="85fd2-246">クエリ ルートに対するすべての操作がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-246">All operations on the query root are supported.</span></span> <span data-ttu-id="85fd2-247">これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="85fd2-248">OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="85fd2-249">コレクション プロジェクションを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="85fd2-249">Split queries with collection projections</span></span>

<span data-ttu-id="85fd2-250">`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="85fd2-251">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="85fd2-252">この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="85fd2-253">サポートされるのはコレクションの具体化のみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="85fd2-254">上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="85fd2-255">この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="85fd2-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="85fd2-256">IndexAttribute</span></span>

<span data-ttu-id="85fd2-257">新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="85fd2-258">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="85fd2-259">SQL Server の場合、移行によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="85fd2-260">IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="85fd2-261">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-261">For example:</span></span>

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

<span data-ttu-id="85fd2-262">SQL Server の場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="85fd2-263">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="85fd2-264">クエリ変換の例外の改善</span><span class="sxs-lookup"><span data-stu-id="85fd2-264">Improved query translation exceptions</span></span>

<span data-ttu-id="85fd2-265">クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="85fd2-266">たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="85fd2-267">EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="85fd2-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="85fd2-268">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-268">Unhandled exception.</span></span> <span data-ttu-id="85fd2-269">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.IsSigned)' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="85fd2-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="85fd2-270">追加情報:エンティティ型 'Artist' のメンバー 'IsSigned' の変換に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="85fd2-271">指定されたメンバーがマップされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="85fd2-272">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="85fd2-273">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="85fd2-274">同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="85fd2-275">たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="85fd2-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="85fd2-276">EF Core では、次の例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="85fd2-277">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-277">Unhandled exception.</span></span> <span data-ttu-id="85fd2-278">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="85fd2-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="85fd2-279">追加情報:'StringComparison' 引数を受け取る 'string.Equals' メソッドの変換はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="85fd2-280">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2129535 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="85fd2-281">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="85fd2-282">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="85fd2-283">トランザクション ID を指定する</span><span class="sxs-lookup"><span data-stu-id="85fd2-283">Specify transaction ID</span></span>

<span data-ttu-id="85fd2-284">この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="85fd2-285">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="85fd2-286">EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="85fd2-287">この ID は通常、トランザクションの開始時に EF Core によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="85fd2-288">アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="85fd2-289">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="85fd2-290">IPAddress マッピング</span><span class="sxs-lookup"><span data-stu-id="85fd2-290">IPAddress mapping</span></span>

<span data-ttu-id="85fd2-291">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="85fd2-292">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="85fd2-293">標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="85fd2-294">たとえば、次のエンティティ型をマップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="85fd2-295">SQL Server では、移行によって次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="85fd2-296">これで、通常の方法でエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="85fd2-297">結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="85fd2-298">スキャフォールディング時の OnConfiguring を除外する</span><span class="sxs-lookup"><span data-stu-id="85fd2-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="85fd2-299">DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="85fd2-300">ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="85fd2-301">これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="85fd2-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="85fd2-303">または、パッケージ マネージャー コンソールで:</span><span class="sxs-lookup"><span data-stu-id="85fd2-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="85fd2-304">[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85fd2-304">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="85fd2-305">文字列の FirstOrDefault の変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="85fd2-306">この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="85fd2-307">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="85fd2-308">FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="85fd2-309">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="85fd2-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="85fd2-310">は、SQL Server を使用すると、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="85fd2-311">CASE ブロックの簡略化</span><span class="sxs-lookup"><span data-stu-id="85fd2-311">Simplify case blocks</span></span>

<span data-ttu-id="85fd2-312">EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="85fd2-313">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="85fd2-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="85fd2-314">は、SQL Server では、正式には次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-314">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="85fd2-315">しかし、今後は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="85fd2-316">Preview 5</span><span class="sxs-lookup"><span data-stu-id="85fd2-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="85fd2-317">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="85fd2-317">Database collations</span></span>

<span data-ttu-id="85fd2-318">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="85fd2-319">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="85fd2-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="85fd2-320">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="85fd2-321">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="85fd2-322">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="85fd2-323">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="85fd2-324">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="85fd2-325">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="85fd2-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="85fd2-326">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="85fd2-327">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="85fd2-328">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="85fd2-329">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="85fd2-330">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="85fd2-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="85fd2-331">引数は、コマンド ラインから [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="85fd2-332">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="85fd2-333">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="85fd2-334">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="85fd2-335">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="85fd2-336">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="85fd2-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="85fd2-337">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="85fd2-338">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="85fd2-339">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="85fd2-340">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="85fd2-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="85fd2-341">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="85fd2-342">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="85fd2-343">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="85fd2-344">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="85fd2-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="85fd2-345">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="85fd2-346">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="85fd2-347">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="85fd2-348">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="85fd2-349">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="85fd2-350">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="85fd2-350">SQLite computed columns</span></span>

<span data-ttu-id="85fd2-351">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="85fd2-352">Preview 4</span><span class="sxs-lookup"><span data-stu-id="85fd2-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="85fd2-353">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="85fd2-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="85fd2-354">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="85fd2-355">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="85fd2-356">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="85fd2-357">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="85fd2-358">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="85fd2-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="85fd2-359">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="85fd2-360">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="85fd2-361">Preview 3</span><span class="sxs-lookup"><span data-stu-id="85fd2-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="85fd2-362">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="85fd2-362">Filtered Include</span></span>

<span data-ttu-id="85fd2-363">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="85fd2-364">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="85fd2-365">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="85fd2-366">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="85fd2-367">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="85fd2-368">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="85fd2-369">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="85fd2-370">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="85fd2-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="85fd2-371">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="85fd2-372">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="85fd2-373">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="85fd2-374">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="85fd2-375">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="85fd2-376">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="85fd2-377">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="85fd2-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="85fd2-378">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="85fd2-379">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="85fd2-380">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="85fd2-381">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="85fd2-382">詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85fd2-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="85fd2-383">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="85fd2-384">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="85fd2-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="85fd2-385">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="85fd2-386">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="85fd2-387">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="85fd2-388">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-388">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="85fd2-389">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="85fd2-390">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="85fd2-390">Cosmos partition keys</span></span>

<span data-ttu-id="85fd2-391">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="85fd2-392">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="85fd2-393">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="85fd2-394">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="85fd2-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="85fd2-395">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="85fd2-396">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-396">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="85fd2-397">Preview 2</span><span class="sxs-lookup"><span data-stu-id="85fd2-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="85fd2-398">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="85fd2-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="85fd2-399">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="85fd2-400">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="85fd2-401">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-401">For example:</span></span>

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

<span data-ttu-id="85fd2-402">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="85fd2-403">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="85fd2-403">Complete discriminator mapping</span></span>

<span data-ttu-id="85fd2-404">EF Core では、[継承階層の TPH マッピング](/ef/core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="85fd2-405">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="85fd2-406">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="85fd2-407">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="85fd2-408">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="85fd2-409">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="85fd2-410">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="85fd2-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="85fd2-411">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="85fd2-412">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="85fd2-413">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="85fd2-414">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="85fd2-415">Preview 1</span><span class="sxs-lookup"><span data-stu-id="85fd2-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="85fd2-416">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="85fd2-416">Simple logging</span></span>

<span data-ttu-id="85fd2-417">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="85fd2-418">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="85fd2-419">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="85fd2-420">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="85fd2-421">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="85fd2-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="85fd2-422">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="85fd2-423">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="85fd2-424">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="85fd2-425">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="85fd2-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="85fd2-426">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="85fd2-427">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="85fd2-428">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="85fd2-429">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="85fd2-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="85fd2-430">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="85fd2-431">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="85fd2-432">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="85fd2-433">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="85fd2-434">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="85fd2-434">Change-tracking proxies</span></span>

<span data-ttu-id="85fd2-435">EF Core で、[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="85fd2-436">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="85fd2-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="85fd2-437">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="85fd2-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="85fd2-438">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="85fd2-439">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="85fd2-439">Enhanced debug views</span></span>

<span data-ttu-id="85fd2-440">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="85fd2-441">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="85fd2-442">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="85fd2-443">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="85fd2-444">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="85fd2-445">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="85fd2-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="85fd2-446">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="85fd2-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="85fd2-447">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="85fd2-448">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="85fd2-449">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="85fd2-450">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="85fd2-451">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="85fd2-451">Indexer properties</span></span>

<span data-ttu-id="85fd2-452">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="85fd2-453">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="85fd2-454">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="85fd2-455">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="85fd2-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="85fd2-456">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="85fd2-457">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="85fd2-458">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="85fd2-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="85fd2-459">IsRelational</span></span>

<span data-ttu-id="85fd2-460">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="85fd2-461">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="85fd2-462">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="85fd2-463">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="85fd2-464">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="85fd2-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="85fd2-465">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="85fd2-466">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="85fd2-467">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](/ef/core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="85fd2-468">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="85fd2-469">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="85fd2-470">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="85fd2-471">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="85fd2-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="85fd2-472">DateDiffWeek</span></span>
* <span data-ttu-id="85fd2-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="85fd2-473">DateFromParts</span></span>

<span data-ttu-id="85fd2-474">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="85fd2-475">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="85fd2-476">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="85fd2-477">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="85fd2-478">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="85fd2-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="85fd2-479">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="85fd2-480">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-480">Query translation for Reverse</span></span>

<span data-ttu-id="85fd2-481">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="85fd2-482">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85fd2-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="85fd2-483">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="85fd2-484">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="85fd2-485">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="85fd2-486">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="85fd2-487">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="85fd2-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="85fd2-488">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85fd2-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="85fd2-489">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85fd2-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
