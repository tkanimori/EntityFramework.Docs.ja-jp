---
title: 複数のプロバイダーを使用した移行-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: 384ad27e405adc2bccb5e96aae30e5bd7ac556be
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824713"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="ede8e-102">複数のプロバイダーを使用した移行</span><span class="sxs-lookup"><span data-stu-id="ede8e-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="ede8e-103">[EF Core ツール][1]は、アクティブなプロバイダーの移行のみをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ede8e-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="ede8e-104">ただし、場合によっては、DbContext を使用して、複数のプロバイダー (Microsoft SQL Server や SQLite など) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ede8e-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="ede8e-105">移行でこれを処理する方法は2つあります。</span><span class="sxs-lookup"><span data-stu-id="ede8e-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="ede8e-106">2つの移行セット (プロバイダーごとに1つ) を維持することも、両方で動作する1つのセットに統合することもできます。</span><span class="sxs-lookup"><span data-stu-id="ede8e-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="ede8e-107">2つの移行セット</span><span class="sxs-lookup"><span data-stu-id="ede8e-107">Two migration sets</span></span>

<span data-ttu-id="ede8e-108">最初の方法では、モデルの変更ごとに2つの移行を生成します。</span><span class="sxs-lookup"><span data-stu-id="ede8e-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="ede8e-109">これを行う1つの方法は、各移行セットを[個別のアセンブリに][2]配置し、2つの移行を追加する間に、アクティブなプロバイダー (および移行アセンブリ) を手動で切り替えることです。</span><span class="sxs-lookup"><span data-stu-id="ede8e-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="ede8e-110">ツールを簡単に操作できるもう1つの方法は、DbContext から派生した新しい型を作成し、アクティブなプロバイダーをオーバーライドすることです。</span><span class="sxs-lookup"><span data-stu-id="ede8e-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="ede8e-111">この型は、移行を追加または適用するときに、デザイン時に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ede8e-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="ede8e-112">各移行セットは独自の DbContext 型を使用するため、この方法では個別の移行アセンブリを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ede8e-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="ede8e-113">新しい移行を追加する場合は、コンテキストの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="ede8e-113">When adding new migration, specify the context types.</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="ede8e-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ede8e-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="ede8e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ede8e-115">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="ede8e-116">後続の移行では、最後のディレクトリの兄弟として作成されるため、出力ディレクトリを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ede8e-116">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="ede8e-117">1つの移行セット</span><span class="sxs-lookup"><span data-stu-id="ede8e-117">One migration set</span></span>

<span data-ttu-id="ede8e-118">2つの移行セットを作成したくない場合は、両方のプロバイダーに適用できる1つのセットに手動で組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="ede8e-118">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="ede8e-119">認識できない注釈がプロバイダーによって無視されるため、注釈を共存させることができます。</span><span class="sxs-lookup"><span data-stu-id="ede8e-119">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="ede8e-120">たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ede8e-120">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="ede8e-121">操作を1つのプロバイダーにのみ適用できる (またはプロバイダー間で異なる) 場合は、`ActiveProvider` プロパティを使用して、どのプロバイダーがアクティブであるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="ede8e-121">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
