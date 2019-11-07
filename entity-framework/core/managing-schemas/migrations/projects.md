---
title: 個別の移行プロジェクトの使用-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 0c08855db77470d28e23f9ef1d147497dfcdff83
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655551"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="d6c7f-102">個別の移行プロジェクトの使用</span><span class="sxs-lookup"><span data-stu-id="d6c7f-102">Using a Separate Migrations Project</span></span>

<span data-ttu-id="d6c7f-103">移行は、`DbContext`を含むアセンブリとは別のアセンブリに格納することができます。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="d6c7f-104">また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="d6c7f-105">目的</span><span class="sxs-lookup"><span data-stu-id="d6c7f-105">To do this...</span></span>

1. <span data-ttu-id="d6c7f-106">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-106">Create a new class library.</span></span>

2. <span data-ttu-id="d6c7f-107">DbContext アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="d6c7f-108">移行およびモデルスナップショットファイルをクラスライブラリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="d6c7f-109">既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="d6c7f-110">移行アセンブリに既存の移行が含まれていない場合、追加移行コマンドで DbContext を見つけることができないため、これが重要になります。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="d6c7f-111">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="d6c7f-112">スタートアップアセンブリから移行アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="d6c7f-113">これによって循環依存関係が発生する場合は、クラスライブラリの出力パスを更新します。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="d6c7f-114">すべてを正しく行った場合は、新しい移行をプロジェクトに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d6c7f-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="d6c7f-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d6c7f-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="d6c7f-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6c7f-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
