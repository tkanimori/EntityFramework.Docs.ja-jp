---
title: 複数のプロジェクトの EF コアでの移行
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 3684e86cce0005056380d89604d038c734054d14
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
ms.locfileid: "27161228"
---
<a name="using-a-separate-project"></a><span data-ttu-id="052e9-102">別のプロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="052e9-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="052e9-103">1 つ含まれているよりも別のアセンブリで使用して移行を格納することがあります、`DbContext`です。</span><span class="sxs-lookup"><span data-stu-id="052e9-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="052e9-104">また、リリースごとにアップグレードとこの戦略などの移行には、複数のセットを維持するために、開発用の 1 つを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="052e9-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="052e9-105">目的</span><span class="sxs-lookup"><span data-stu-id="052e9-105">To do this...</span></span>

1. <span data-ttu-id="052e9-106">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="052e9-106">Create a new class library.</span></span>

2. <span data-ttu-id="052e9-107">DbContext アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="052e9-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="052e9-108">クラス ライブラリに移行およびモデルのスナップショット ファイルを移動します。</span><span class="sxs-lookup"><span data-stu-id="052e9-108">Move the migrations and model snapshot files to the class library.</span></span>
   * <span data-ttu-id="052e9-109">いずれかを追加していない場合は、DbContext プロジェクトに追加しに移動します。</span><span class="sxs-lookup"><span data-stu-id="052e9-109">If you haven't added any, add one to the DbContext project then move it.</span></span>

4. <span data-ttu-id="052e9-110">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="052e9-110">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="052e9-111">スタートアップ アセンブリから、移行アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="052e9-111">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="052e9-112">これにより、循環する依存関係場合、は、クラス ライブラリの出力パスを更新します。</span><span class="sxs-lookup"><span data-stu-id="052e9-112">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStarupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="052e9-113">削除できた場合すべて正しくは、新しい移行プロジェクトに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="052e9-113">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
