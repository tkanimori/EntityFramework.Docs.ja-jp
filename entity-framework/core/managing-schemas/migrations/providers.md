---
title: 複数のプロバイダー - EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
ms.openlocfilehash: 75c055221609679db3f00016b9cb44c6c8c6e473
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488778"
---
<a name="migrations-with-multiple-providers"></a><span data-ttu-id="22ab5-102">複数のプロバイダーを使用した移行</span><span class="sxs-lookup"><span data-stu-id="22ab5-102">Migrations with Multiple Providers</span></span>
==================================
<span data-ttu-id="22ab5-103">[EF Core ツール][ 1]のみ active プロバイダーへの移行をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="22ab5-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="22ab5-104">場合によっては、ただし、可能性がある、DbContext で 1 つ以上のプロバイダー (Microsoft SQL Server、SQLite など) を使用します。</span><span class="sxs-lookup"><span data-stu-id="22ab5-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="22ab5-105">移行でこれを処理するために 2 つの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="22ab5-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="22ab5-106">2 つのセットを維持する移行の両方で機能プロバイダー--またはセットを 1 つにマージごとに 1 つのことができます。</span><span class="sxs-lookup"><span data-stu-id="22ab5-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

<a name="two-migration-sets"></a><span data-ttu-id="22ab5-107">2 つの移行セット</span><span class="sxs-lookup"><span data-stu-id="22ab5-107">Two migration sets</span></span>
------------------
<span data-ttu-id="22ab5-108">最初の方法では、各モデルの変更のための 2 つの移行を生成します。</span><span class="sxs-lookup"><span data-stu-id="22ab5-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="22ab5-109">移行セットごとにはこれを行う方法の 1 つ[別のアセンブリに][ 2] 2 つの移行を追加する間、アクティブなプロバイダー (および移行アセンブリ) を手動で切り替えるとします。</span><span class="sxs-lookup"><span data-stu-id="22ab5-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="22ab5-110">別の方法をツールで作業が容易では、DbContext から派生し、アクティブなプロバイダーをオーバーライドする新しい型を作成します。</span><span class="sxs-lookup"><span data-stu-id="22ab5-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="22ab5-111">この種類はデザイン時に使用を追加または移行を適用したときにします。</span><span class="sxs-lookup"><span data-stu-id="22ab5-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="22ab5-112">各移行セットは、独自の DbContext 型を使用するための個別移行アセンブリを使用してこの方法は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="22ab5-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="22ab5-113">新しい移行を追加する場合は、コンテキストの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="22ab5-113">When adding new migration, specify the context types.</span></span>

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> <span data-ttu-id="22ab5-114">最後の 1 つの兄弟として作成されているので、次の移行の出力ディレクトリを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="22ab5-114">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

<a name="one-migration-set"></a><span data-ttu-id="22ab5-115">1 つの移行セット</span><span class="sxs-lookup"><span data-stu-id="22ab5-115">One migration set</span></span>
-----------------
<span data-ttu-id="22ab5-116">移行の 2 つのセットがない場合は、手動で両方のプロバイダーに適用できる単一セットに結合することができます。</span><span class="sxs-lookup"><span data-stu-id="22ab5-116">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="22ab5-117">注釈は、プロバイダーを理解しない任意のコメントを無視するために共存できます。</span><span class="sxs-lookup"><span data-stu-id="22ab5-117">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="22ab5-118">たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、これのようになります。</span><span class="sxs-lookup"><span data-stu-id="22ab5-118">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="22ab5-119">操作は、1 つのプロバイダーにのみ適用できます (またはプロバイダーの間で異なるにいる) 場合は、使用、`ActiveProvider`プロパティをプロバイダーがアクティブかを確認します。</span><span class="sxs-lookup"><span data-stu-id="22ab5-119">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
