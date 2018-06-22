---
title: 複数のプロバイダーの EF コアと移行
author: bricelam
ms.author: bricelam
ms.date: 11/8/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d950e74ed4cef7d4274aabcf3eda7b0b735574c6
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2018
ms.locfileid: "30002806"
---
<a name="migrations-with-multiple-providers"></a><span data-ttu-id="4cab6-102">複数のプロバイダーと移行</span><span class="sxs-lookup"><span data-stu-id="4cab6-102">Migrations with Multiple Providers</span></span>
==================================
<span data-ttu-id="4cab6-103">[EF コア ツール][ 1]アクティブなプロバイダーへの移行をスキャフォールディングのみです。</span><span class="sxs-lookup"><span data-stu-id="4cab6-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="4cab6-104">場合によっては、ただし、場合がある、DbContext で (たとえば Microsoft SQL Server や SQLite) の複数のプロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="4cab6-105">これに対処する移行で 2 つの方法ができます。</span><span class="sxs-lookup"><span data-stu-id="4cab6-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="4cab6-106">2 つのセットを保持する移行の両方で作業プロバイダー--またはセットを 1 つにマージごとに 1 つのことができます。</span><span class="sxs-lookup"><span data-stu-id="4cab6-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

<a name="two-migration-sets"></a><span data-ttu-id="4cab6-107">2 つの移行セット</span><span class="sxs-lookup"><span data-stu-id="4cab6-107">Two migration sets</span></span>
------------------
<span data-ttu-id="4cab6-108">最初の方法では、各モデルの変更の 2 つの移行を生成します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="4cab6-109">これは 1 つの方法として各移行のセットを格納する[別のアセンブリに][ 2] 2 つの移行を追加する間、アクティブなプロバイダーと移行アセンブリを手動で切り替えるとします。</span><span class="sxs-lookup"><span data-stu-id="4cab6-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="4cab6-110">ツールを使用して作業が容易にもう 1 つの方法では、DbContext から派生し、アクティブなプロバイダーを上書きする新しい型を作成します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="4cab6-111">この型はデザイン時使用の追加または移行を適用するときに時間します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="4cab6-112">各移行セットは、独自の DbContext 型を使用するため、このアプローチは独立した移行アセンブリを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4cab6-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="4cab6-113">新しい移行を追加する場合は、コンテキストの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-113">When adding new migration, specify the context types.</span></span>

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> <span data-ttu-id="4cab6-114">最後の 1 つの兄弟として作成されているので、次の移行の出力ディレクトリを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4cab6-114">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

<a name="one-migration-set"></a><span data-ttu-id="4cab6-115">1 つの移行のセット</span><span class="sxs-lookup"><span data-stu-id="4cab6-115">One migration set</span></span>
-----------------
<span data-ttu-id="4cab6-116">移行の 2 つのセットを持つしない場合に、手動で一緒に両方のプロバイダーに適用できる 1 つのセットにします。</span><span class="sxs-lookup"><span data-stu-id="4cab6-116">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="4cab6-117">注釈は、プロバイダーが理解していないすべての注釈を無視するために共存できます。</span><span class="sxs-lookup"><span data-stu-id="4cab6-117">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="4cab6-118">たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4cab6-118">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="4cab6-119">操作は、1 つのプロバイダーにのみ適用できます (またはプロバイダー間で異なる方法である) を使用して、`ActiveProvider`プロパティをプロバイダーがアクティブに指示します。</span><span class="sxs-lookup"><span data-stu-id="4cab6-119">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
