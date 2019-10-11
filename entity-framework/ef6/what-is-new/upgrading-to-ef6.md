---
title: Entity Framework 6-EF6 へのアップグレード
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 4395a9c117a6cf38e7fc08f11ee689d6fffa6fed
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182097"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="d5a0d-102">Entity Framework 6 へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="d5a0d-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="d5a0d-103">以前のバージョンの EF では、コードは、NuGet パッケージに付属している .NET Framework および帯域外 (OOB) ライブラリ (主に EntityFramework .dll) の一部として出荷されたコアライブラリ (主に system.object) 間で分割されました。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="d5a0d-104">EF6 はコアライブラリからコードを取得し、OOB ライブラリに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="d5a0d-105">これは、EF をオープンソースにして、.NET Framework とは異なるペースで進化できるようにするために必要でした。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="d5a0d-106">その結果、移動された型に対してアプリケーションを再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="d5a0d-107">これは、EF 4.1 以降に付属している DbContext を使用するアプリケーションでは簡単です。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="d5a0d-108">ObjectContext を使用するアプリケーションには、もう少し作業が必要ですが、それでも難しいことはありません。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="d5a0d-109">既存のアプリケーションを EF6 にアップグレードするために必要な作業のチェックリストを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="d5a0d-110">1. EF6 NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="d5a0d-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="d5a0d-111">新しい Entity Framework 6 ランタイムにアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="d5a0d-112">プロジェクトを右クリックし、 **[NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="d5a0d-113">**[オンライン]** タブで **[entityframework]** を選択し、 **[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="d5a0d-114">以前のバージョンの EntityFramework NuGet パッケージがインストールされている場合は、EF6 にアップグレードされます。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="d5a0d-115">または、パッケージマネージャーコンソールから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="d5a0d-116">2. System.object へのアセンブリ参照が削除されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="d5a0d-117">EF6 NuGet パッケージをインストールすると、プロジェクトからへの参照が自動的に削除されます。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="d5a0d-118">3.Ef 6.x コード生成を使用するように EF Designer (EDMX) モデルをスワップする</span><span class="sxs-lookup"><span data-stu-id="d5a0d-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="d5a0d-119">EF デザイナーで作成されたモデルがある場合は、コード生成テンプレートを更新して、EF6 互換コードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a0d-120">現在、Visual Studio 2012 および2013で使用できる EF 6.x DbContext ジェネレーターテンプレートのみがあります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="d5a0d-121">既存のコード生成テンプレートを削除します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="d5a0d-122">これらのファイルには、通常 **\<edmx_file_name\>.tt**と **\<edmx_file_name @ no__t-5 という名前が付けられます。Context.tt**は、ソリューションエクスプローラーの edmx ファイルの下に入れ子になっています。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="d5a0d-123">ソリューションエクスプローラーでテンプレートを選択し、 **del**キーを押して削除することができます。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="d5a0d-124">Web サイトプロジェクトでは、テンプレートは edmx ファイルの下に入れ子になっていませんが、ソリューションエクスプローラーに並べられています。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="d5a0d-125">VB.NET プロジェクトでは、[すべてのファイルを表示] を有効にして、入れ子になったテンプレートファイルを表示できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="d5a0d-126">適切な EF 6.x コード生成テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="d5a0d-127">EF デザイナーでモデルを開き、デザイン画面を右クリックして、 **[コード生成項目の追加...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="d5a0d-128">DbContext API (推奨) を使用している場合は、 **[データ]** タブで**EF 6.X の dbcontext ジェネレーター**を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="d5a0d-129">Visual Studio 2012 を使用している場合は、このテンプレートを使用するために EF 6 ツールをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="d5a0d-130">詳細については、「 [Get Entity Framework](~/ef6/fundamentals/install.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="d5a0d-131">ObjectContext API を使用している場合は、 **[オンライン]** タブを選択し、 **EF 6.X の entityobject Generator**を検索する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="d5a0d-132">コード生成テンプレートにカスタマイズを適用した場合は、更新されたテンプレートに再適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="d5a0d-133">4。使用されているすべてのコア EF 型の名前空間を更新する</span><span class="sxs-lookup"><span data-stu-id="d5a0d-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="d5a0d-134">DbContext および Code First 型の名前空間は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="d5a0d-135">これは、EF 4.1 以降を使用する多くのアプリケーションでは、何も変更する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="d5a0d-136">以前に system.object に含まれていた ObjectContext などの型は、新しい名前空間に移動されました。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="d5a0d-137">つまり、EF6 に対してビルドするには、 *using*ディレクティブまたは*Import*ディレクティブを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="d5a0d-138">名前空間の変更に関する一般的な規則は、system.string 内のすべての型が、system.string に移動されることです。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-138">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="d5a0d-139">言い換えると、" **Entity. Core** " を挿入するだけです。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="d5a0d-140">System. Data の後。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-140">after System.Data.</span></span> <span data-ttu-id="d5a0d-141">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-141">For example:</span></span>

- <span data-ttu-id="d5a0d-142">System.string. EntityException = > system.string。**Entity. Core**。EntityException</span><span class="sxs-lookup"><span data-stu-id="d5a0d-142">System.Data.EntityException => System.Data.**Entity.Core**.EntityException</span></span>  
- <span data-ttu-id="d5a0d-143">System.string は、system.string を > します。**Entity. Core**。オブジェクト。 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="d5a0d-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core**.Objects.ObjectContext</span></span>  
- <span data-ttu-id="d5a0d-144">RelationshipManager = > system.string のデータを持つことができます。**Entity. Core**。オブジェクト. RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="d5a0d-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="d5a0d-145">これらの型は、ほとんどの DbContext ベースのアプリケーションでは直接使用されないため、*コア*名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="d5a0d-146">System.object の一部であった型の中には、DbContext ベースのアプリケーションで一般的に使用されているものもあります。そのため、*コア*名前空間に移動されていません。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="d5a0d-147">これらの数値は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-147">These are:</span></span>

- <span data-ttu-id="d5a0d-148">EntityState = > system.string をします。**エンティティ**。EntityState</span><span class="sxs-lookup"><span data-stu-id="d5a0d-148">System.Data.EntityState => System.Data.**Entity**.EntityState</span></span>  
- <span data-ttu-id="d5a0d-149">System.string. EdmFunctionAttribute = > system.string......**Entity. DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="d5a0d-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="d5a0d-150">このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="d5a0d-151">System.string. EntityFunctions = > system.string です。**Entity DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="d5a0d-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="d5a0d-152">このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています)。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="d5a0d-153">空間クラス (例: DbGeography, Dbgeography) は、system.string > から移動しています。**エンティティ**。許容</span><span class="sxs-lookup"><span data-stu-id="d5a0d-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity**.Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="d5a0d-154">System.string 名前空間の一部の型は、EF アセンブリではない system.string に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="d5a0d-155">これらの型は移動されていないため、名前空間は変更されません。</span><span class="sxs-lookup"><span data-stu-id="d5a0d-155">These types have not moved and so their namespaces remain unchanged.</span></span>
