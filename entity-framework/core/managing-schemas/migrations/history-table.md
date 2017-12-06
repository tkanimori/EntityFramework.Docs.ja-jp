---
title: "カスタムの移行履歴テーブルな EF コア"
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: cb9892241f3d7f1fae6293bd60a8a5c3e7120969
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="custom-migrations-history-table"></a><span data-ttu-id="b4c59-102">カスタムの移行履歴テーブル</span><span class="sxs-lookup"><span data-stu-id="b4c59-102">Custom Migrations History Table</span></span>
===============================
<span data-ttu-id="b4c59-103">既定では、EF コアの追跡でという名前のテーブルに記録して、移行の種類をデータベースに適用されている`__EFMigrationsHistory`です。</span><span class="sxs-lookup"><span data-stu-id="b4c59-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="b4c59-104">さまざまな理由で、ニーズに合わせて次の表をカスタマイズする場合があります。</span><span class="sxs-lookup"><span data-stu-id="b4c59-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b4c59-105">移行履歴テーブルをカスタマイズする場合は*後*移行を適用する必要があります、データベースの既存のテーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="b4c59-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

<a name="schema-and-table-name"></a><span data-ttu-id="b4c59-106">スキーマとテーブル名</span><span class="sxs-lookup"><span data-stu-id="b4c59-106">Schema and table name</span></span>
----------------------
<span data-ttu-id="b4c59-107">スキーマとを使用してテーブル名を変更することができます、`MigrationsHistoryTable()`メソッド`OnConfiguring()`(または`ConfigureServices()`ASP.NET Core 上)。</span><span class="sxs-lookup"><span data-stu-id="b4c59-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="b4c59-108">SQL Server の EF コア プロバイダーを使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b4c59-108">Here is an example using the SQL Server EF Core provider.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a><span data-ttu-id="b4c59-109">その他の変更</span><span class="sxs-lookup"><span data-stu-id="b4c59-109">Other changes</span></span>
-------------
<span data-ttu-id="b4c59-110">テーブルの追加の側面を構成するのに上書きし、プロバイダー固有の置換`IHistoryRepository`サービス。</span><span class="sxs-lookup"><span data-stu-id="b4c59-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="b4c59-111">MigrationId 列名を変更する例を次に示します*Id* SQL サーバーにします。</span><span class="sxs-lookup"><span data-stu-id="b4c59-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> <span data-ttu-id="b4c59-112">`SqlServerHistoryRepository`内部の名前空間内部にあるし、将来のリリースで変更可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b4c59-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

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
