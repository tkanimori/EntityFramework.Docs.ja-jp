---
title: カスタムの移行履歴テーブル - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
ms.openlocfilehash: 1a253972a8f4e410421ec8a77c079e588d368819
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488817"
---
<a name="custom-migrations-history-table"></a><span data-ttu-id="c04d1-102">カスタムの移行履歴テーブル</span><span class="sxs-lookup"><span data-stu-id="c04d1-102">Custom Migrations History Table</span></span>
===============================
<span data-ttu-id="c04d1-103">既定では、EF Core の追跡でという名前のテーブルに記録して、移行をデータベースに適用されている`__EFMigrationsHistory`します。</span><span class="sxs-lookup"><span data-stu-id="c04d1-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="c04d1-104">さまざまな理由から、ニーズに合うように、このテーブルをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="c04d1-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c04d1-105">移行履歴テーブルをカスタマイズする場合は*後*移行を適用する必要があります、データベースの既存のテーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c04d1-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

<a name="schema-and-table-name"></a><span data-ttu-id="c04d1-106">スキーマとテーブル名</span><span class="sxs-lookup"><span data-stu-id="c04d1-106">Schema and table name</span></span>
----------------------
<span data-ttu-id="c04d1-107">スキーマと名前を使用してテーブルを変更することができます、`MigrationsHistoryTable()`メソッド`OnConfiguring()`(または`ConfigureServices()`の ASP.NET core)。</span><span class="sxs-lookup"><span data-stu-id="c04d1-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="c04d1-108">SQL Server EF Core プロバイダーを使用する例を示します。</span><span class="sxs-lookup"><span data-stu-id="c04d1-108">Here is an example using the SQL Server EF Core provider.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a><span data-ttu-id="c04d1-109">その他の変更</span><span class="sxs-lookup"><span data-stu-id="c04d1-109">Other changes</span></span>
-------------
<span data-ttu-id="c04d1-110">表に、その他の側面を構成するには、オーバーライドおよびプロバイダーに固有の置換`IHistoryRepository`サービス。</span><span class="sxs-lookup"><span data-stu-id="c04d1-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="c04d1-111">MigrationId 列名を変更する例を次に示します*Id* SQL サーバーにします。</span><span class="sxs-lookup"><span data-stu-id="c04d1-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> <span data-ttu-id="c04d1-112">`SqlServerHistoryRepository` 内部の名前空間内で、将来のリリースで変更可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c04d1-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

``` csharp
class MyHistoryRepository : SqlServerHistoryRepository
{
    public MyHistoryRepository(HistoryRepositoryDependencies dependencies)
        : base(dependencies)
    {
    }

    protected override void ConfigureTable(EntityTypeBuilder<HistoryRow> history)
    {
        base.ConfigureTable(history);

        history.Property(h => h.MigrationId).HasColumnName("Id");
    }
}
```
