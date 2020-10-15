---
title: Entity Framework 6-EF6 へのアップグレード
description: Entity Framework 6 へのアップグレード
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: 14cd0e259832377332f77a199502699d75982bb4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064251"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="6618b-103">Entity Framework 6 へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="6618b-103">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="6618b-104">以前のバージョンの EF では、コードは、NuGet パッケージに付属している .NET Framework および帯域外 (主に EntityFramework.dll) ライブラリの一部として出荷されたコアライブラリ (主に System.Data.Entity.dll) と分けていました。</span><span class="sxs-lookup"><span data-stu-id="6618b-104">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="6618b-105">EF6 はコアライブラリからコードを取得し、OOB ライブラリに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="6618b-105">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="6618b-106">これは、EF をオープンソースにして、.NET Framework とは異なるペースで進化できるようにするために必要でした。</span><span class="sxs-lookup"><span data-stu-id="6618b-106">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="6618b-107">その結果、移動された型に対してアプリケーションを再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-107">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="6618b-108">これは、EF 4.1 以降に付属している DbContext を使用するアプリケーションでは簡単です。</span><span class="sxs-lookup"><span data-stu-id="6618b-108">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="6618b-109">ObjectContext を使用するアプリケーションには、もう少し作業が必要ですが、それでも難しいことはありません。</span><span class="sxs-lookup"><span data-stu-id="6618b-109">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="6618b-110">既存のアプリケーションを EF6 にアップグレードするために必要な作業のチェックリストを次に示します。</span><span class="sxs-lookup"><span data-stu-id="6618b-110">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="6618b-111">1. EF6 NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="6618b-111">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="6618b-112">新しい Entity Framework 6 ランタイムにアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-112">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="6618b-113">プロジェクトを右クリックし、[ **NuGet パッケージの管理...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6618b-113">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="6618b-114">[**オンライン**] タブで [ **entityframework** ] を選択し、[**インストール**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6618b-114">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="6618b-115">以前のバージョンの EntityFramework NuGet パッケージがインストールされている場合は、EF6 にアップグレードされます。</span><span class="sxs-lookup"><span data-stu-id="6618b-115">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="6618b-116">または、パッケージマネージャーコンソールから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6618b-116">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="6618b-117">2. System.Data.Entity.dll へのアセンブリ参照が削除されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6618b-117">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="6618b-118">EF6 NuGet パッケージをインストールすると、プロジェクトからへの参照が自動的に削除されます。</span><span class="sxs-lookup"><span data-stu-id="6618b-118">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="6618b-119">3. ef Designer (EDMX) モデルをスワップして EF 6.x コード生成を使用する</span><span class="sxs-lookup"><span data-stu-id="6618b-119">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="6618b-120">EF デザイナーで作成されたモデルがある場合は、コード生成テンプレートを更新して、EF6 互換コードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-120">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="6618b-121">現在、Visual Studio 2012 および2013で使用できる EF 6.x DbContext ジェネレーターテンプレートのみがあります。</span><span class="sxs-lookup"><span data-stu-id="6618b-121">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="6618b-122">既存のコード生成テンプレートを削除します。</span><span class="sxs-lookup"><span data-stu-id="6618b-122">Delete existing code-generation templates.</span></span> <span data-ttu-id="6618b-123">これらのファイルは、通常は\*\* \<edmx_file_name\> .tt**とという名前になり**ます。 \<edmx_file_name\>Context.tt\*\*は、ソリューションエクスプローラーの edmx ファイルの下に入れ子になっています。</span><span class="sxs-lookup"><span data-stu-id="6618b-123">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="6618b-124">ソリューションエクスプローラーでテンプレートを選択し、 **del** キーを押して削除することができます。</span><span class="sxs-lookup"><span data-stu-id="6618b-124">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="6618b-125">Web サイトプロジェクトでは、テンプレートは edmx ファイルの下に入れ子になっていませんが、ソリューションエクスプローラーに並べられています。</span><span class="sxs-lookup"><span data-stu-id="6618b-125">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="6618b-126">VB.NET プロジェクトでは、[すべてのファイルを表示] を有効にして、入れ子になったテンプレートファイルを表示できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-126">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="6618b-127">適切な EF 6.x コード生成テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="6618b-127">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="6618b-128">EF デザイナーでモデルを開き、デザイン画面を右クリックして、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6618b-128">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="6618b-129">DbContext API (推奨) を使用している場合は、[**データ**] タブで**EF 6.X の dbcontext ジェネレーター**を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6618b-129">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="6618b-130">Visual Studio 2012 を使用している場合は、このテンプレートを使用するために EF 6 ツールをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-130">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="6618b-131">詳細については、「 [Get Entity Framework](xref:ef6/fundamentals/install) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6618b-131">See [Get Entity Framework](xref:ef6/fundamentals/install) for details.</span></span>  

    - <span data-ttu-id="6618b-132">ObjectContext API を使用している場合は、[ **オンライン** ] タブを選択し、 **EF 6.X の entityobject Generator**を検索する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-132">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="6618b-133">コード生成テンプレートにカスタマイズを適用した場合は、更新されたテンプレートに再適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-133">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="6618b-134">4. 使用されているすべてのコア EF 型の名前空間を更新する</span><span class="sxs-lookup"><span data-stu-id="6618b-134">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="6618b-135">DbContext および Code First 型の名前空間は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="6618b-135">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="6618b-136">これは、EF 4.1 以降を使用する多くのアプリケーションでは、何も変更する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="6618b-136">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="6618b-137">以前は System.Data.Entity.dll であった ObjectContext のような型は、新しい名前空間に移動されました。</span><span class="sxs-lookup"><span data-stu-id="6618b-137">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="6618b-138">つまり、EF6 に対してビルドするには、 *using* ディレクティブまたは *Import* ディレクティブを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6618b-138">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="6618b-139">名前空間の変更に関する一般的な規則は、system.string 内のすべての型が、system.string に移動されることです。</span><span class="sxs-lookup"><span data-stu-id="6618b-139">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="6618b-140">言い換えると、" **Entity. Core** " を挿入するだけです。</span><span class="sxs-lookup"><span data-stu-id="6618b-140">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="6618b-141">System. Data の後。</span><span class="sxs-lookup"><span data-stu-id="6618b-141">after System.Data.</span></span> <span data-ttu-id="6618b-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6618b-142">For example:</span></span>

- <span data-ttu-id="6618b-143">System.string. EntityException => system.string。**Entity. Core**。EntityException</span><span class="sxs-lookup"><span data-stu-id="6618b-143">System.Data.EntityException => System.Data.**Entity.Core**.EntityException</span></span>  
- <span data-ttu-id="6618b-144">System.string は、system.string を> します。**Entity. Core**。オブジェクト。 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="6618b-144">System.Data.Objects.ObjectContext => System.Data.**Entity.Core**.Objects.ObjectContext</span></span>  
- <span data-ttu-id="6618b-145">RelationshipManager => system.string のデータを持つことができます。**Entity. Core**。オブジェクト. RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="6618b-145">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="6618b-146">これらの型は、ほとんどの DbContext ベースのアプリケーションでは直接使用されないため、 *コア* 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="6618b-146">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="6618b-147">System.Data.Entity.dll の一部であった一部の型は、DbContext ベースのアプリケーションに対してよく使用され、直接使用されるため、 *コア* 名前空間に移動されていません。</span><span class="sxs-lookup"><span data-stu-id="6618b-147">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="6618b-148">次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6618b-148">These are:</span></span>

- <span data-ttu-id="6618b-149">EntityState => system.string をします。**エンティティ**。EntityState</span><span class="sxs-lookup"><span data-stu-id="6618b-149">System.Data.EntityState => System.Data.**Entity**.EntityState</span></span>  
- <span data-ttu-id="6618b-150">System.string. EdmFunctionAttribute => system.string......**Entity. DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="6618b-150">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="6618b-151">このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています。</span><span class="sxs-lookup"><span data-stu-id="6618b-151">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="6618b-152">System.string. EntityFunctions => system.string です。**Entity DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="6618b-152">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="6618b-153">このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています)。</span><span class="sxs-lookup"><span data-stu-id="6618b-153">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="6618b-154">空間クラス (例: DbGeography, Dbgeography) は、system.string> から移動しています。**エンティティ**。許容</span><span class="sxs-lookup"><span data-stu-id="6618b-154">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity**.Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="6618b-155">System.string 名前空間の一部の型は、EF アセンブリではない System.Data.dll にあります。</span><span class="sxs-lookup"><span data-stu-id="6618b-155">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="6618b-156">これらの型は移動されていないため、名前空間は変更されません。</span><span class="sxs-lookup"><span data-stu-id="6618b-156">These types have not moved and so their namespaces remain unchanged.</span></span>
