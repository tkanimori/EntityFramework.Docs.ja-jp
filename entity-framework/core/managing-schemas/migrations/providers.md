---
title: 複数のプロバイダーを使用した移行-EF Core
description: Entity Framework Core で複数のデータベースプロバイダーを対象とする場合に、移行を使用してデータベーススキーマを管理する
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429781"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="d3762-103">複数のプロバイダーを使用した移行</span><span class="sxs-lookup"><span data-stu-id="d3762-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="d3762-104">[EF Core ツール](xref:core/cli/index)は、アクティブなプロバイダーの移行のみをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="d3762-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="d3762-105">ただし、場合によっては、DbContext を使用して、複数のプロバイダー (Microsoft SQL Server や SQLite など) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d3762-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="d3762-106">これを処理するには、複数の移行セット (プロバイダーごとに1つ) を保持し、各モデルの変更に対してそれぞれに移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="d3762-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="d3762-107">複数のコンテキスト型の使用</span><span class="sxs-lookup"><span data-stu-id="d3762-107">Using multiple context types</span></span>

<span data-ttu-id="d3762-108">複数の移行セットを作成する方法の1つは、プロバイダーごとに1つの DbContext 型を使用することです。</span><span class="sxs-lookup"><span data-stu-id="d3762-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="d3762-109">新しい移行を追加するときに、コンテキストの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="d3762-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d3762-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3762-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="d3762-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3762-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="d3762-112">後続の移行では、最後のディレクトリの兄弟として作成されるため、出力ディレクトリを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d3762-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="d3762-113">1つのコンテキスト型の使用</span><span class="sxs-lookup"><span data-stu-id="d3762-113">Using one context type</span></span>

<span data-ttu-id="d3762-114">DbContext 型を1つ使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d3762-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="d3762-115">現時点では、移行を別のアセンブリに移動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d3762-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="d3762-116">プロジェクトの設定手順については [、「個別の移行プロジェクトの使用](xref:core/managing-schemas/migrations/projects) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3762-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="d3762-117">この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="d3762-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="d3762-118">EF Core 5.0 以降では、ツールからアプリに引数を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d3762-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="d3762-119">これにより、ツールの実行時にプロジェクトを手動で変更しなくても済むように、ワークフローをより効率的にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d3762-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="d3762-120">ここでは、 [汎用ホスト](/dotnet/core/extensions/generic-host)を使用する場合に適切に機能する1つのパターンを示します。</span><span class="sxs-lookup"><span data-stu-id="d3762-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="d3762-121">既定のホストビルダーはコマンドライン引数から構成を読み取るため、ツールの実行時にプロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d3762-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d3762-122">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3762-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="d3762-123">この `--` トークンは、 `dotnet ef` 後続のすべてのものを引数として処理し、オプションとして解析しないように指示します。</span><span class="sxs-lookup"><span data-stu-id="d3762-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="d3762-124">によって使用されていない追加 `dotnet ef` の引数は、アプリに転送されます。</span><span class="sxs-lookup"><span data-stu-id="d3762-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="d3762-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3762-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="d3762-126">アプリの追加の引数を指定する機能が EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="d3762-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="d3762-127">以前のバージョンを使用している場合は、代わりに環境変数で構成値を指定してください。</span><span class="sxs-lookup"><span data-stu-id="d3762-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
