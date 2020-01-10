---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 12/17/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: bab725afffe1fbf9f8c0abeef58579ac9dc842d2
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502083"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="66d2e-102">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="66d2e-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="66d2e-103">以下のツールと拡張機能は、Entity Framework Core 2.1 およびそれ以降の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="66d2e-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="66d2e-104">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="66d2e-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="66d2e-105">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="66d2e-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="66d2e-106">特に、古いバージョンの EF Core 用に構築された拡張機能は、最新バージョンで動作させる前に更新が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="66d2e-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="66d2e-107">ツール</span><span class="sxs-lookup"><span data-stu-id="66d2e-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="66d2e-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="66d2e-108">LLBLGen Pro</span></span>

<span data-ttu-id="66d2e-109">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="66d2e-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="66d2e-110">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="66d2e-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="66d2e-111">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-111">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-112">Web サイト</span><span class="sxs-lookup"><span data-stu-id="66d2e-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="66d2e-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="66d2e-113">Devart Entity Developer</span></span>

<span data-ttu-id="66d2e-114">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザインです。</span><span class="sxs-lookup"><span data-stu-id="66d2e-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="66d2e-115">モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="66d2e-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="66d2e-116">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-116">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-117">Web サイト</span><span class="sxs-lookup"><span data-stu-id="66d2e-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="66d2e-118">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="66d2e-118">EF Core Power Tools</span></span>

<span data-ttu-id="66d2e-119">EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="66d2e-119">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="66d2e-120">既存のデータベースと [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="66d2e-120">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="66d2e-121">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-121">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-122">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="66d2e-122">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="66d2e-123">Entity Framework のビジュアル エディター</span><span class="sxs-lookup"><span data-stu-id="66d2e-123">Entity Framework Visual Editor</span></span>

<span data-ttu-id="66d2e-124">Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの ORM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="66d2e-124">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="66d2e-125">コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="66d2e-125">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="66d2e-126">継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="66d2e-126">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="66d2e-127">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-127">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-128">Marketplace</span><span class="sxs-lookup"><span data-stu-id="66d2e-128">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="66d2e-129">CatFactory</span><span class="sxs-lookup"><span data-stu-id="66d2e-129">CatFactory</span></span>

<span data-ttu-id="66d2e-130">CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。</span><span class="sxs-lookup"><span data-stu-id="66d2e-130">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="66d2e-131">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-131">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-132">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-132">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="66d2e-133">LoreSoft の Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="66d2e-133">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="66d2e-134">Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="66d2e-134">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="66d2e-135">このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="66d2e-135">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="66d2e-136">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-136">For EF Core: 2.</span></span>

<span data-ttu-id="66d2e-137">[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="66d2e-137">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="66d2e-138">拡張機能</span><span class="sxs-lookup"><span data-stu-id="66d2e-138">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="66d2e-139">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="66d2e-139">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="66d2e-140">EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="66d2e-140">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="66d2e-141">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-141">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-142">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-142">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="66d2e-143">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="66d2e-143">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="66d2e-144">同じクエリの後続の実行によってデータベースにアクセスされたり、キャッシュからデータが直接取得されたりしないように、2 番目のレベルのキャッシュに EF Core クエリの結果が格納されるようにする拡張機能。</span><span class="sxs-lookup"><span data-stu-id="66d2e-144">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span> <span data-ttu-id="66d2e-145">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-145">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-146">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-146">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="geco"></a><span data-ttu-id="66d2e-147">Geco</span><span class="sxs-lookup"><span data-stu-id="66d2e-147">Geco</span></span>

<span data-ttu-id="66d2e-148">Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="66d2e-148">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="66d2e-149">Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="66d2e-149">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="66d2e-150">シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。</span><span class="sxs-lookup"><span data-stu-id="66d2e-150">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="66d2e-151">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-151">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-152">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-152">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="66d2e-153">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="66d2e-153">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="66d2e-154">Entity Framework Core ツールチェーンと Handlebars テンプレートを使用して、既存のデータベースからリバース エンジニアリングされたクラスのカスタマイズを可能にします。</span><span class="sxs-lookup"><span data-stu-id="66d2e-154">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="66d2e-155">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-155">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-156">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-156">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="66d2e-157">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="66d2e-157">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="66d2e-158">NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。</span><span class="sxs-lookup"><span data-stu-id="66d2e-158">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="66d2e-159">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-159">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-160">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-160">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="66d2e-161">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="66d2e-161">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="66d2e-162">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="66d2e-162">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="66d2e-163">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-163">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-164">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-164">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="66d2e-165">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="66d2e-165">EFCore.BulkExtensions</span></span>

<span data-ttu-id="66d2e-166">一括操作 (挿入、更新、削除) の EF Core 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="66d2e-166">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="66d2e-167">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-167">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-168">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-168">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="66d2e-169">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="66d2e-169">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="66d2e-170">デザイン時の複数形化を追加します。</span><span class="sxs-lookup"><span data-stu-id="66d2e-170">Adds design-time pluralization.</span></span> <span data-ttu-id="66d2e-171">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-171">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-172">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-172">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="66d2e-173">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="66d2e-173">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="66d2e-174">[Index] 属性のリバイバルです (モデル構築のための拡張機能を含む)。</span><span class="sxs-lookup"><span data-stu-id="66d2e-174">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="66d2e-175">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-176">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-176">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="66d2e-177">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="66d2e-177">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="66d2e-178">EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。</span><span class="sxs-lookup"><span data-stu-id="66d2e-178">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="66d2e-179">リレーショナル プロバイダーのような動作になります。</span><span class="sxs-lookup"><span data-stu-id="66d2e-179">Makes it act more like a relational provider.</span></span> <span data-ttu-id="66d2e-180">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-180">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-181">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-181">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="66d2e-182">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="66d2e-182">EFCore.TemporalSupport</span></span>

<span data-ttu-id="66d2e-183">テンポラル サポートの実装。</span><span class="sxs-lookup"><span data-stu-id="66d2e-183">An implementation of temporal support.</span></span> <span data-ttu-id="66d2e-184">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-184">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-185">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-185">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="66d2e-186">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="66d2e-186">EfCoreTemporalTable</span></span>

<span data-ttu-id="66d2e-187">導入された拡張メソッド `AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)` を使用して、任意のデータベース上で簡単にテンポラル クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="66d2e-187">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="66d2e-188">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="66d2e-188">For EF Core: 3.</span></span>

[<span data-ttu-id="66d2e-189">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-189">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="66d2e-190">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="66d2e-190">EFCore.TimeTraveler</span></span>

<span data-ttu-id="66d2e-191">EF Core コード、エンティティ、および既に定義済みのマッピングを使用して、[SQL Server テンポラル履歴](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)に対するフル機能の Entity Framework Core クエリを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="66d2e-191">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="66d2e-192">コードを `using (TemporalQuery.AsOf(targetDateTime)) {...}` でラップすることにより、時間に関係なく使用できます。</span><span class="sxs-lookup"><span data-stu-id="66d2e-192">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="66d2e-193">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="66d2e-193">For EF Core: 3.</span></span>

[<span data-ttu-id="66d2e-194">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-194">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="66d2e-195">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="66d2e-195">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="66d2e-196">SQL Server を使用している開発者がテンポラル テーブルを簡単に使用できるようにする、Entity Framework Core の拡張ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="66d2e-196">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="66d2e-197">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-197">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-198">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-198">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="66d2e-199">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="66d2e-199">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="66d2e-200">高パフォーマンスな第 2 レベルのクエリ キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="66d2e-200">A high-performance second-level query cache.</span></span> <span data-ttu-id="66d2e-201">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="66d2e-201">For EF Core: 2.</span></span>

[<span data-ttu-id="66d2e-202">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="66d2e-202">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="66d2e-203">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="66d2e-203">Entity Framework Plus</span></span>

<span data-ttu-id="66d2e-204">次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。</span><span class="sxs-lookup"><span data-stu-id="66d2e-204">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="66d2e-205">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-205">For EF Core: 2, 3.</span></span>

<span data-ttu-id="66d2e-206">[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="66d2e-206">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="66d2e-207">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="66d2e-207">Entity Framework Extensions</span></span>

<span data-ttu-id="66d2e-208">高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。</span><span class="sxs-lookup"><span data-stu-id="66d2e-208">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="66d2e-209">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="66d2e-209">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="66d2e-210">Web サイト</span><span class="sxs-lookup"><span data-stu-id="66d2e-210">Website</span></span>](https://entityframework-extensions.net/)
