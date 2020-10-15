---
title: 個別の移行プロジェクトの使用-EF Core
description: Entity Framework Core でのデータベーススキーマの管理に個別の移行プロジェクトを使用する
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062491"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="b9f07-103">個別の移行プロジェクトの使用</span><span class="sxs-lookup"><span data-stu-id="b9f07-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="b9f07-104">移行は、を含むアセンブリとは別のアセンブリに格納することをお勧めし `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="b9f07-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="b9f07-105">また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。</span><span class="sxs-lookup"><span data-stu-id="b9f07-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="b9f07-106">目的</span><span class="sxs-lookup"><span data-stu-id="b9f07-106">To do this...</span></span>

1. <span data-ttu-id="b9f07-107">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-107">Create a new class library.</span></span>

2. <span data-ttu-id="b9f07-108">DbContext アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="b9f07-109">移行およびモデルスナップショットファイルをクラスライブラリに移動します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="b9f07-110">既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="b9f07-111">移行アセンブリに既存の移行が含まれていない場合、Add-Migration コマンドで DbContext を見つけることができないため、これは重要です。</span><span class="sxs-lookup"><span data-stu-id="b9f07-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="b9f07-112">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-112">Configure the migrations assembly:</span></span>

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="b9f07-113">スタートアップアセンブリから移行アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="b9f07-114">これによって循環依存関係が発生する場合は、クラスライブラリの出力パスを更新します。</span><span class="sxs-lookup"><span data-stu-id="b9f07-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="b9f07-115">すべてを正しく行った場合は、新しい移行をプロジェクトに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b9f07-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="b9f07-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9f07-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="b9f07-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9f07-117">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
