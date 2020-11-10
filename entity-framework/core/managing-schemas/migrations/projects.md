---
title: 個別の移行プロジェクトの使用-EF Core
description: Entity Framework Core でのデータベーススキーマの管理に個別の移行プロジェクトを使用する
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429807"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="e3dd4-103">個別の移行プロジェクトの使用</span><span class="sxs-lookup"><span data-stu-id="e3dd4-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="e3dd4-104">移行は、を含むプロジェクトとは別のプロジェクトに保存することができ `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="e3dd4-105">また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="e3dd4-106">この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="e3dd4-107">手順</span><span class="sxs-lookup"><span data-stu-id="e3dd4-107">Steps</span></span>

1. <span data-ttu-id="e3dd4-108">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-108">Create a new class library.</span></span>

2. <span data-ttu-id="e3dd4-109">DbContext プロジェクトへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="e3dd4-110">移行およびモデルスナップショットファイルをクラスライブラリに移動します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="e3dd4-111">既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="e3dd4-112">これは、移行プロジェクトに既存の移行が含まれていない場合に、Add-Migration コマンドで DbContext を見つけることができないため、重要です。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="e3dd4-113">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="e3dd4-114">[ **スタートアップ** プロジェクトから移行プロジェクトへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="e3dd4-115">これによって循環依存関係が発生した場合は、代わりに **移行** プロジェクトの基本出力パスを更新できます。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="e3dd4-116">すべてを正しく行った場合は、新しい移行をプロジェクトに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e3dd4-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="e3dd4-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e3dd4-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="e3dd4-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3dd4-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
