---
title: Entity Framework 6 へのアップグレード
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 2e2dacfe67238bdb7fd1f31f784319049f0f2cb0
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490949"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="ccd94-102">Entity Framework 6 へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="ccd94-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="ccd94-103">以前のバージョンの EF コードは、.NET Framework の一部として出荷されるコア ライブラリ (主に system.data.entity.dll 内) と NuGet パッケージに付属して帯域外の (OOB) ライブラリ (主に EntityFramework.dll) の間で分割が。</span><span class="sxs-lookup"><span data-stu-id="ccd94-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="ccd94-104">EF6 では、コア ライブラリからコードを取得し、OOB ライブラリを組み込みます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="ccd94-105">これはオープン ソースに EF を許可するために必要と .NET Framework からのさまざまなペースで進化できるようにします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="ccd94-106">この結果は、アプリケーションが移動された型に対して再構築する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="ccd94-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="ccd94-107">これは、EF 4.1 以降を発送済みとしての DbContext を使用しているアプリケーションの簡単なはずです。</span><span class="sxs-lookup"><span data-stu-id="ccd94-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="ccd94-108">ObjectContext を使用するアプリケーションの必要なもう少し作業がまだは難しくはありません。</span><span class="sxs-lookup"><span data-stu-id="ccd94-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="ccd94-109">既存のアプリケーションを EF6 にアップグレードするために必要のある事柄のチェックリストを次に示します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="ccd94-110">1.EF6 の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="ccd94-111">新しい Entity Framework 6 のランタイムをアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd94-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="ccd94-112">クリックし、プロジェクトを右クリックして**NuGet パッケージの管理.**</span><span class="sxs-lookup"><span data-stu-id="ccd94-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="ccd94-113">下、**オンライン** タブを選択**EntityFramework**クリック**インストール**</span><span class="sxs-lookup"><span data-stu-id="ccd94-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="ccd94-114">以前のバージョンの EntityFramework NuGet パッケージがインストールされている場合は、EF6 にアップグレードこのされます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="ccd94-115">または、パッケージ マネージャー コンソールから、次のコマンドを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="ccd94-116">2.System.Data.Entity.dll へのアセンブリ参照を削除することを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="ccd94-117">EF6 の NuGet パッケージをインストールする必要があります自動的に削除 System.Data.Entity へのプロジェクトから参照できます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="ccd94-118">3.EF 6.x のコード生成を使用するすべての EF デザイナー (EDMX) モデルをスワップします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="ccd94-119">EF Designer で作成されたモデルがあれば、EF6 の互換性のあるコードを生成するコード生成テンプレートを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd94-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="ccd94-120">現在は EF 6.x DbContext ジェネレーター テンプレートのみ Visual Studio 2012 および 2013 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="ccd94-121">既存のコード生成テンプレートを削除します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="ccd94-122">これらのファイルの名前は通常 **\<edmx_file_name\>.tt**と **\<edmx_file_name\>します。Context.tt**ソリューション エクスプ ローラーで、edmx ファイルの下に入れ子にするとします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="ccd94-123">キーを押して、ソリューション エクスプ ローラーで、テンプレートを選択することができます、 **Del**それらを削除するキー。</span><span class="sxs-lookup"><span data-stu-id="ccd94-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="ccd94-124">Web サイト プロジェクトで、テンプレートはことはありません、edmx ファイルの下で入れ子になったがソリューション エクスプ ローラーで一緒に表示します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="ccd94-125">VB.NET プロジェクトでは、' すべてのファイル ' 入れ子になったテンプレート ファイルを参照できるを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd94-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="ccd94-126">適切な EF 6.x のコード生成テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="ccd94-127">デザイン画面を選択しますを右クリックが EF デザイナーでモデルを開く**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccd94-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="ccd94-128">(推奨)、DbContext API を使用しているかどうかは**EF 6.x DbContext ジェネレーター**で使用できるが、**データ** タブ。</span><span class="sxs-lookup"><span data-stu-id="ccd94-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="ccd94-129">Visual Studio 2012 を使用している場合は、このテンプレートを使用して EF 6 ツールをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd94-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="ccd94-130">参照してください[Entity Framework の取得](~/ef6/fundamentals/install.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="ccd94-131">ObjectContext API を使用しているかどうかは、選択する必要があります、**オンライン**タブし、検索**EF 6.x EntityObject ジェネレーター**します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="ccd94-132">コード生成テンプレートをすべてのカスタマイズを適用した場合は、更新されたテンプレートに再適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd94-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="ccd94-133">4.使用されている任意の中核となる EF 型の名前空間を更新します。</span><span class="sxs-lookup"><span data-stu-id="ccd94-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="ccd94-134">DbContext とコードの最初の型の名前空間は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="ccd94-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="ccd94-135">つまり、EF 4.1 を使用する多くのアプリケーションまたは後で何も変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ccd94-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="ccd94-136">System.data.entity.dll 内に含まれていた ObjectContext などの型は、新しい名前空間に移動されました。</span><span class="sxs-lookup"><span data-stu-id="ccd94-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="ccd94-137">つまり、更新する必要があります、*を使用して*または*インポート*EF6 に対してビルドするためのディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="ccd94-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="ccd94-138">名前空間の変更の一般的な規則は、System.Data.\* で任意の型が System.Data.Entity.Core.\* に移動されます。</span><span class="sxs-lookup"><span data-stu-id="ccd94-138">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="ccd94-139">つまり、挿入**Entity.Core します。**</span><span class="sxs-lookup"><span data-stu-id="ccd94-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="ccd94-140">System.Data 後。</span><span class="sxs-lookup"><span data-stu-id="ccd94-140">after System.Data.</span></span> <span data-ttu-id="ccd94-141">例えば:</span><span class="sxs-lookup"><span data-stu-id="ccd94-141">For example:</span></span>

- <span data-ttu-id="ccd94-142">System.Data.EntityException = > System.Data します。**Entity.Core します。** EntityException</span><span class="sxs-lookup"><span data-stu-id="ccd94-142">System.Data.EntityException => System.Data.**Entity.Core.** EntityException</span></span>  
- <span data-ttu-id="ccd94-143">System.Data.Objects.ObjectContext = > System.Data します。**Entity.Core します。** Objects.ObjectContext</span><span class="sxs-lookup"><span data-stu-id="ccd94-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core.** Objects.ObjectContext</span></span>  
- <span data-ttu-id="ccd94-144">System.Data.Objects.DataClasses.RelationshipManager = > System.Data します。**Entity.Core します。** Objects.DataClasses.RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="ccd94-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core.** Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="ccd94-145">これらの種類が、 *Core*名前空間 DbContext ベースのほとんどのアプリケーションを直接使用されていないためです。</span><span class="sxs-lookup"><span data-stu-id="ccd94-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="ccd94-146">System.data.entity.dll 内の一部であった一部の種類は引き続き使用一般的と直接 DbContext ベースのアプリケーションとためありませんに移動されました、 *Core*名前空間。</span><span class="sxs-lookup"><span data-stu-id="ccd94-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="ccd94-147">これらの数値は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ccd94-147">These are:</span></span>

- <span data-ttu-id="ccd94-148">System.Data.EntityState = > System.Data します。**エンティティ。** EntityState</span><span class="sxs-lookup"><span data-stu-id="ccd94-148">System.Data.EntityState => System.Data.**Entity.** EntityState</span></span>  
- <span data-ttu-id="ccd94-149">System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data します。**Entity.DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="ccd94-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="ccd94-150">このクラスの名前が変更されました古い名前のクラスは引き続き存在して、動作しますが、ここで不使用とマークします。</span><span class="sxs-lookup"><span data-stu-id="ccd94-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="ccd94-151">System.Data.Objects.EntityFunctions = > System.Data します。**Entity.DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="ccd94-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="ccd94-152">このクラスの名前が変更されました古い名前のクラスは引き続き存在して、動作しますが、現在不使用とマークされ)。</span><span class="sxs-lookup"><span data-stu-id="ccd94-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="ccd94-153">System.Data.Spatial から移動空間のクラス (たとえば、DbGeography、DbGeometry) = > System.Data します。**エンティティ。** 空間</span><span class="sxs-lookup"><span data-stu-id="ccd94-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity.** Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="ccd94-154">System.Data 名前空間の一部の型は System.Data.dll EF アセンブリではないです。</span><span class="sxs-lookup"><span data-stu-id="ccd94-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="ccd94-155">これらの型に移動していないと、そのため、名前空間は変更されません。</span><span class="sxs-lookup"><span data-stu-id="ccd94-155">These types have not moved and so their namespaces remain unchanged.</span></span>
