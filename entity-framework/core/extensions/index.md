---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 12/17/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e3806f7161fecfe66450d3e08f97caf3d2c84cf3
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634239"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="79d3f-102">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="79d3f-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="79d3f-103">以下のツールと拡張機能は、Entity Framework Core 2.1 およびそれ以降の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="79d3f-104">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="79d3f-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="79d3f-105">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="79d3f-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="79d3f-106">特に、古いバージョンの EF Core 用に構築された拡張機能は、最新バージョンで動作させる前に更新が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="79d3f-107">ツール</span><span class="sxs-lookup"><span data-stu-id="79d3f-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="79d3f-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="79d3f-108">LLBLGen Pro</span></span>

<span data-ttu-id="79d3f-109">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="79d3f-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="79d3f-110">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="79d3f-111">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-111">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-112">Web サイト</span><span class="sxs-lookup"><span data-stu-id="79d3f-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="79d3f-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="79d3f-113">Devart Entity Developer</span></span>

<span data-ttu-id="79d3f-114">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザインです。</span><span class="sxs-lookup"><span data-stu-id="79d3f-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="79d3f-115">モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="79d3f-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="79d3f-116">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-116">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-117">Web サイト</span><span class="sxs-lookup"><span data-stu-id="79d3f-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="79d3f-118">Entity Framework 用の nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="79d3f-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="79d3f-119">厳密に型指定された、Entity Framework 用に拡張できるクラスを作成する ORM です。</span><span class="sxs-lookup"><span data-stu-id="79d3f-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="79d3f-120">生成されたコードは Entity Framework Core になります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="79d3f-121">これらに違いはありません。</span><span class="sxs-lookup"><span data-stu-id="79d3f-121">There is no difference.</span></span> <span data-ttu-id="79d3f-122">これは、EF またはカスタム ORM に代わるものではありません。</span><span class="sxs-lookup"><span data-stu-id="79d3f-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="79d3f-123">チームが複雑なデータベース スキーマを管理できるビジュアルのモデリング レイヤーです。</span><span class="sxs-lookup"><span data-stu-id="79d3f-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="79d3f-124">これは、Git のような SCM ソフトウェアに適しており、最小限の競合でご利用のモデルへのマルチ ユーザー アクセスが可能になります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="79d3f-125">インストーラーによって、モデルの変更が追跡され、アップグレード スクリプトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="79d3f-126">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="79d3f-126">For EF Core: 3.</span></span>

[<span data-ttu-id="79d3f-127">GitHub サイト</span><span class="sxs-lookup"><span data-stu-id="79d3f-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="79d3f-128">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="79d3f-128">EF Core Power Tools</span></span>

<span data-ttu-id="79d3f-129">EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="79d3f-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="79d3f-130">既存のデータベースと [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="79d3f-131">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-132">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="79d3f-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="79d3f-133">Entity Framework のビジュアル エディター</span><span class="sxs-lookup"><span data-stu-id="79d3f-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="79d3f-134">Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの ORM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="79d3f-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="79d3f-135">コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="79d3f-136">継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="79d3f-137">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-137">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="79d3f-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="79d3f-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="79d3f-139">CatFactory</span></span>

<span data-ttu-id="79d3f-140">CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。</span><span class="sxs-lookup"><span data-stu-id="79d3f-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="79d3f-141">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-141">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-142">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="79d3f-143">LoreSoft の Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="79d3f-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="79d3f-144">Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="79d3f-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="79d3f-145">このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="79d3f-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="79d3f-146">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-146">For EF Core: 2.</span></span>

<span data-ttu-id="79d3f-147">[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="79d3f-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="79d3f-148">拡張機能</span><span class="sxs-lookup"><span data-stu-id="79d3f-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="79d3f-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="79d3f-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="79d3f-150">EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="79d3f-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="79d3f-151">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-151">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-152">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="79d3f-153">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="79d3f-153">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="79d3f-154">同じクエリの後続の実行によってデータベースにアクセスされたり、キャッシュからデータが直接取得されたりしないように、2 番目のレベルのキャッシュに EF Core クエリの結果が格納されるようにする拡張機能。</span><span class="sxs-lookup"><span data-stu-id="79d3f-154">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span> <span data-ttu-id="79d3f-155">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-155">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-156">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-156">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="geco"></a><span data-ttu-id="79d3f-157">Geco</span><span class="sxs-lookup"><span data-stu-id="79d3f-157">Geco</span></span>

<span data-ttu-id="79d3f-158">Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="79d3f-158">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="79d3f-159">Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="79d3f-159">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="79d3f-160">シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-160">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="79d3f-161">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-161">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-162">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-162">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="79d3f-163">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="79d3f-163">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="79d3f-164">Entity Framework Core ツールチェーンと Handlebars テンプレートを使用して、既存のデータベースからリバース エンジニアリングされたクラスのカスタマイズを可能にします。</span><span class="sxs-lookup"><span data-stu-id="79d3f-164">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="79d3f-165">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-165">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-166">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-166">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="79d3f-167">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="79d3f-167">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="79d3f-168">NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。</span><span class="sxs-lookup"><span data-stu-id="79d3f-168">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="79d3f-169">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-169">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-170">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-170">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="79d3f-171">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="79d3f-171">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="79d3f-172">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="79d3f-172">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="79d3f-173">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-173">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-174">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-174">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="79d3f-175">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="79d3f-175">EFCore.BulkExtensions</span></span>

<span data-ttu-id="79d3f-176">一括操作 (挿入、更新、削除) の EF Core 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="79d3f-176">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="79d3f-177">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-177">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-178">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-178">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="79d3f-179">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="79d3f-179">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="79d3f-180">デザイン時の複数形化を追加します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-180">Adds design-time pluralization.</span></span> <span data-ttu-id="79d3f-181">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-181">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-182">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-182">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="79d3f-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="79d3f-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="79d3f-184">[Index] 属性のリバイバルです (モデル構築のための拡張機能を含む)。</span><span class="sxs-lookup"><span data-stu-id="79d3f-184">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="79d3f-185">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-185">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-186">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-186">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="79d3f-187">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="79d3f-187">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="79d3f-188">EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-188">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="79d3f-189">リレーショナル プロバイダーのような動作になります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-189">Makes it act more like a relational provider.</span></span> <span data-ttu-id="79d3f-190">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-190">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-191">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-191">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="79d3f-192">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="79d3f-192">EFCore.TemporalSupport</span></span>

<span data-ttu-id="79d3f-193">テンポラル サポートの実装。</span><span class="sxs-lookup"><span data-stu-id="79d3f-193">An implementation of temporal support.</span></span> <span data-ttu-id="79d3f-194">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-194">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-195">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-195">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="79d3f-196">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="79d3f-196">EfCoreTemporalTable</span></span>

<span data-ttu-id="79d3f-197">導入された拡張メソッド `AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)` を使用して、任意のデータベース上で簡単にテンポラル クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-197">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="79d3f-198">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="79d3f-198">For EF Core: 3.</span></span>

[<span data-ttu-id="79d3f-199">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-199">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="79d3f-200">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="79d3f-200">EFCore.TimeTraveler</span></span>

<span data-ttu-id="79d3f-201">EF Core コード、エンティティ、および既に定義済みのマッピングを使用して、[SQL Server テンポラル履歴](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)に対するフル機能の Entity Framework Core クエリを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="79d3f-201">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="79d3f-202">コードを `using (TemporalQuery.AsOf(targetDateTime)) {...}` でラップすることにより、時間に関係なく使用できます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-202">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="79d3f-203">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="79d3f-203">For EF Core: 3.</span></span>

[<span data-ttu-id="79d3f-204">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-204">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="79d3f-205">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="79d3f-205">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="79d3f-206">SQL Server を使用している開発者がテンポラル テーブルを簡単に使用できるようにする、Entity Framework Core の拡張ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="79d3f-206">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="79d3f-207">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-207">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-208">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-208">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="79d3f-209">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="79d3f-209">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="79d3f-210">高パフォーマンスな第 2 レベルのクエリ キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="79d3f-210">A high-performance second-level query cache.</span></span> <span data-ttu-id="79d3f-211">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="79d3f-211">For EF Core: 2.</span></span>

[<span data-ttu-id="79d3f-212">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-212">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="79d3f-213">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="79d3f-213">Entity Framework Plus</span></span>

<span data-ttu-id="79d3f-214">次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。</span><span class="sxs-lookup"><span data-stu-id="79d3f-214">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="79d3f-215">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-215">For EF Core: 2, 3.</span></span>

<span data-ttu-id="79d3f-216">[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="79d3f-216">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="79d3f-217">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="79d3f-217">Entity Framework Extensions</span></span>

<span data-ttu-id="79d3f-218">高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。</span><span class="sxs-lookup"><span data-stu-id="79d3f-218">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="79d3f-219">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="79d3f-219">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="79d3f-220">Web サイト</span><span class="sxs-lookup"><span data-stu-id="79d3f-220">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="79d3f-221">Expressionify</span><span class="sxs-lookup"><span data-stu-id="79d3f-221">Expressionify</span></span>

<span data-ttu-id="79d3f-222">LINQ ラムダ式で拡張メソッドを呼び出すためのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-222">Add support for calling extension methods in linq lambdas.</span></span> <span data-ttu-id="79d3f-223">対象の EF Core:3.1</span><span class="sxs-lookup"><span data-stu-id="79d3f-223">For EF Core: 3.1</span></span>

[<span data-ttu-id="79d3f-224">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="79d3f-224">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="79d3f-225">XLinq</span><span class="sxs-lookup"><span data-stu-id="79d3f-225">XLinq</span></span>

<span data-ttu-id="79d3f-226">リレーショナル データベース用の統合言語クエリ (LINQ) テクノロジ。</span><span class="sxs-lookup"><span data-stu-id="79d3f-226">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="79d3f-227">C# を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="79d3f-227">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="79d3f-228">対象の EF Core:3.1</span><span class="sxs-lookup"><span data-stu-id="79d3f-228">For EF Core: 3.1</span></span>

- <span data-ttu-id="79d3f-229">クエリの作成時のラムダ、変数、関数内での複数のステートメントなどの C# の完全なサポート。</span><span class="sxs-lookup"><span data-stu-id="79d3f-229">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="79d3f-230">SQL と意味的な違いはありません。</span><span class="sxs-lookup"><span data-stu-id="79d3f-230">No semantic gap with SQL.</span></span> <span data-ttu-id="79d3f-231">XLinq では SQL ステートメント (`SELECT`、`FROM`、`WHERE`) を第一級の C# メソッドとして、使い慣れた構文を intellisense、タイプ セーフおよびリファクタリングと組み合わせ宣言します。</span><span class="sxs-lookup"><span data-stu-id="79d3f-231">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="79d3f-232">その結果、SQL はその API をローカルに公開する、文字どおりの *"統合言語 SQL"* である "もう 1 つの" クラス ライブラリになります。</span><span class="sxs-lookup"><span data-stu-id="79d3f-232">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="79d3f-233">Web サイト</span><span class="sxs-lookup"><span data-stu-id="79d3f-233">Website</span></span>](http://xlinq.live/)
