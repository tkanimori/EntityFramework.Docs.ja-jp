---
title: 複数のプロジェクトの EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 30a6afad1488e74ce2585be3d780186311379a97
ms.sourcegitcommit: ad1bdea58ed35d0f19791044efe9f72f94189c18
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47447145"
---
<a name="using-a-separate-project"></a><span data-ttu-id="9584e-102">別のプロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="9584e-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="9584e-103">1 つを含む別のアセンブリで、移行を格納することも、`DbContext`します。</span><span class="sxs-lookup"><span data-stu-id="9584e-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="9584e-104">リリースごとにアップグレードとこの戦略など、移行の複数のセットを維持するために、開発用に 1 つを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9584e-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="9584e-105">目的</span><span class="sxs-lookup"><span data-stu-id="9584e-105">To do this...</span></span>

1. <span data-ttu-id="9584e-106">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9584e-106">Create a new class library.</span></span>

2. <span data-ttu-id="9584e-107">DbContext アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="9584e-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="9584e-108">クラス ライブラリに移行し、モデルのスナップショット ファイルを移動します。</span><span class="sxs-lookup"><span data-stu-id="9584e-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="9584e-109">既存の移行がない場合は、DbContext を含むプロジェクトを生成し、移動します。</span><span class="sxs-lookup"><span data-stu-id="9584e-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span> <span data-ttu-id="9584e-110">移行アセンブリに既存の移行が含まれていない場合、Add-migration コマンドことはできません、DbContext を検索するため重要です。</span><span class="sxs-lookup"><span data-stu-id="9584e-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="9584e-111">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9584e-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="9584e-112">スタートアップ アセンブリから、移行のアセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="9584e-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="9584e-113">これにより、循環依存関係場合は、クラス ライブラリの出力パスを更新します。</span><span class="sxs-lookup"><span data-stu-id="9584e-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="9584e-114">すべてのプロセスが正しく、プロジェクトに新しい移行を追加できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="9584e-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
