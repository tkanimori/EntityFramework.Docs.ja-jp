---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 246cf687f2a087e9a9a569c875b27712ebe80c5c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538331"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="4f590-102">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="4f590-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="4f590-103">以下のツールと拡張機能は、Entity Framework Core 2.1 およびそれ以降の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="4f590-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="4f590-104">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="4f590-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="4f590-105">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="4f590-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="4f590-106">特に、古いバージョンの EF Core 用に構築された拡張機能は、最新バージョンで動作させる前に更新が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4f590-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="4f590-107">ツール</span><span class="sxs-lookup"><span data-stu-id="4f590-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="4f590-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="4f590-108">LLBLGen Pro</span></span>

<span data-ttu-id="4f590-109">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="4f590-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="4f590-110">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="4f590-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="4f590-111">対象の EF Core:2、3</span><span class="sxs-lookup"><span data-stu-id="4f590-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="4f590-112">Web サイト</span><span class="sxs-lookup"><span data-stu-id="4f590-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="4f590-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="4f590-113">Devart Entity Developer</span></span>

<span data-ttu-id="4f590-114">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザインです。</span><span class="sxs-lookup"><span data-stu-id="4f590-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="4f590-115">モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4f590-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="4f590-116">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-116">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-117">Web サイト</span><span class="sxs-lookup"><span data-stu-id="4f590-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="4f590-118">Entity Framework 用の nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="4f590-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="4f590-119">厳密に型指定された、Entity Framework 用に拡張できるクラスを作成する ORM です。</span><span class="sxs-lookup"><span data-stu-id="4f590-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="4f590-120">生成されたコードは Entity Framework Core になります。</span><span class="sxs-lookup"><span data-stu-id="4f590-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="4f590-121">これらに違いはありません。</span><span class="sxs-lookup"><span data-stu-id="4f590-121">There is no difference.</span></span> <span data-ttu-id="4f590-122">これは、EF またはカスタム ORM に代わるものではありません。</span><span class="sxs-lookup"><span data-stu-id="4f590-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="4f590-123">チームが複雑なデータベース スキーマを管理できるビジュアルのモデリング レイヤーです。</span><span class="sxs-lookup"><span data-stu-id="4f590-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="4f590-124">これは、Git のような SCM ソフトウェアに適しており、最小限の競合でご利用のモデルへのマルチ ユーザー アクセスが可能になります。</span><span class="sxs-lookup"><span data-stu-id="4f590-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="4f590-125">インストーラーによって、モデルの変更が追跡され、アップグレード スクリプトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f590-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="4f590-126">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-126">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-127">GitHub サイト</span><span class="sxs-lookup"><span data-stu-id="4f590-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="4f590-128">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="4f590-128">EF Core Power Tools</span></span>

<span data-ttu-id="4f590-129">EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="4f590-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="4f590-130">既存のデータベースと [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f590-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="4f590-131">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-132">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="4f590-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="4f590-133">Entity Framework のビジュアル エディター</span><span class="sxs-lookup"><span data-stu-id="4f590-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="4f590-134">Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの ORM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="4f590-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="4f590-135">コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="4f590-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="4f590-136">継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4f590-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="4f590-137">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-137">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="4f590-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="4f590-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="4f590-139">CatFactory</span></span>

<span data-ttu-id="4f590-140">CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。</span><span class="sxs-lookup"><span data-stu-id="4f590-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="4f590-141">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-141">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-142">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="4f590-143">LoreSoft の Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="4f590-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="4f590-144">Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f590-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="4f590-145">このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f590-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="4f590-146">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-146">For EF Core: 2.</span></span>

<span data-ttu-id="4f590-147">[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="4f590-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="4f590-148">拡張機能</span><span class="sxs-lookup"><span data-stu-id="4f590-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="4f590-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="4f590-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="4f590-150">EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="4f590-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="4f590-151">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-151">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-152">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="4f590-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="4f590-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="4f590-154">第 2 レベルのキャッシュは、クエリ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="4f590-154">Second level caching is a query cache.</span></span> <span data-ttu-id="4f590-155">EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。</span><span class="sxs-lookup"><span data-stu-id="4f590-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="4f590-156">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-156">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-157">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="4f590-158">Geco</span><span class="sxs-lookup"><span data-stu-id="4f590-158">Geco</span></span>

<span data-ttu-id="4f590-159">Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="4f590-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="4f590-160">Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4f590-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="4f590-161">シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f590-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="4f590-162">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-162">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-163">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="4f590-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="4f590-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="4f590-165">Entity Framework Core ツールチェーンと Handlebars テンプレートを使用して、既存のデータベースからリバース エンジニアリングされたクラスのカスタマイズを可能にします。</span><span class="sxs-lookup"><span data-stu-id="4f590-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="4f590-166">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-167">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="4f590-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4f590-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="4f590-169">NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。</span><span class="sxs-lookup"><span data-stu-id="4f590-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="4f590-170">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-171">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="4f590-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="4f590-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="4f590-173">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="4f590-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="4f590-174">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-174">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-175">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="4f590-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="4f590-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="4f590-177">一括操作 (挿入、更新、削除) の EF Core 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="4f590-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="4f590-178">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-179">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="4f590-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="4f590-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="4f590-181">デザイン時の複数形化を追加します。</span><span class="sxs-lookup"><span data-stu-id="4f590-181">Adds design-time pluralization.</span></span> <span data-ttu-id="4f590-182">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-182">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-183">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="4f590-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="4f590-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="4f590-185">[Index] 属性のリバイバルです (モデル構築のための拡張機能を含む)。</span><span class="sxs-lookup"><span data-stu-id="4f590-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="4f590-186">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-187">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="4f590-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="4f590-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="4f590-189">EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。</span><span class="sxs-lookup"><span data-stu-id="4f590-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="4f590-190">リレーショナル プロバイダーのような動作になります。</span><span class="sxs-lookup"><span data-stu-id="4f590-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="4f590-191">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-191">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-192">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="4f590-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="4f590-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="4f590-194">テンポラル サポートの実装。</span><span class="sxs-lookup"><span data-stu-id="4f590-194">An implementation of temporal support.</span></span> <span data-ttu-id="4f590-195">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-195">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-196">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="4f590-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="4f590-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="4f590-198">導入された拡張メソッド `AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)` を使用して、任意のデータベース上で簡単にテンポラル クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4f590-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="4f590-199">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-199">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-200">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="4f590-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="4f590-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="4f590-202">EF Core コード、エンティティ、および既に定義済みのマッピングを使用して、[SQL Server テンポラル履歴](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)に対するフル機能の Entity Framework Core クエリを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="4f590-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="4f590-203">コードを `using (TemporalQuery.AsOf(targetDateTime)) {...}` でラップすることにより、時間に関係なく使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f590-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="4f590-204">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-204">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-205">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="4f590-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="4f590-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="4f590-207">SQL Server を使用している開発者がテンポラル テーブルを簡単に使用できるようにする、Entity Framework Core の拡張ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="4f590-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="4f590-208">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-208">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-209">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="4f590-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="4f590-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="4f590-211">高パフォーマンスな第 2 レベルのクエリ キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="4f590-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="4f590-212">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="4f590-212">For EF Core: 2.</span></span>

[<span data-ttu-id="4f590-213">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="4f590-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="4f590-214">Entity Framework Plus</span></span>

<span data-ttu-id="4f590-215">次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。</span><span class="sxs-lookup"><span data-stu-id="4f590-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="4f590-216">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="4f590-217">[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="4f590-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="4f590-218">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="4f590-218">Entity Framework Extensions</span></span>

<span data-ttu-id="4f590-219">高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。</span><span class="sxs-lookup"><span data-stu-id="4f590-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="4f590-220">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-221">Web サイト</span><span class="sxs-lookup"><span data-stu-id="4f590-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="4f590-222">Expressionify</span><span class="sxs-lookup"><span data-stu-id="4f590-222">Expressionify</span></span>

<span data-ttu-id="4f590-223">LINQ ラムダ式で拡張メソッドを呼び出すためのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4f590-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="4f590-224">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-224">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-225">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="4f590-226">XLinq</span><span class="sxs-lookup"><span data-stu-id="4f590-226">XLinq</span></span>

<span data-ttu-id="4f590-227">リレーショナル データベース用の統合言語クエリ (LINQ) テクノロジ。</span><span class="sxs-lookup"><span data-stu-id="4f590-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="4f590-228">C# を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="4f590-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="4f590-229">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-229">For EF Core: 3.</span></span>

- <span data-ttu-id="4f590-230">クエリの作成時のラムダ、変数、関数内での複数のステートメントなどの C# の完全なサポート。</span><span class="sxs-lookup"><span data-stu-id="4f590-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="4f590-231">SQL と意味的な違いはありません。</span><span class="sxs-lookup"><span data-stu-id="4f590-231">No semantic gap with SQL.</span></span> <span data-ttu-id="4f590-232">XLinq では SQL ステートメント (`SELECT`、`FROM`、`WHERE`) を第一級の C# メソッドとして、使い慣れた構文を intellisense、タイプ セーフおよびリファクタリングと組み合わせ宣言します。</span><span class="sxs-lookup"><span data-stu-id="4f590-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="4f590-233">その結果、SQL はその API をローカルに公開する、文字どおりの *"統合言語 SQL"* である "もう 1 つの" クラス ライブラリになります。</span><span class="sxs-lookup"><span data-stu-id="4f590-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="4f590-234">Web サイト</span><span class="sxs-lookup"><span data-stu-id="4f590-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="4f590-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="4f590-235">Ramses</span></span>

<span data-ttu-id="4f590-236">ライフサイクル フック (SaveChanges 用)。</span><span class="sxs-lookup"><span data-stu-id="4f590-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="4f590-237">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="4f590-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="4f590-238">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="4f590-239">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="4f590-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="4f590-240">これにより、snake_case を含むすべてのテーブル名と列名が自動的にすべて大文字またはすべて小文字の名前になります。</span><span class="sxs-lookup"><span data-stu-id="4f590-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="4f590-241">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-241">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-242">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="4f590-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="4f590-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="4f590-244">NodaTime の SQL Server 用 EntityFrameworkCore にネイティブ サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4f590-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="4f590-245">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-245">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-246">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="4f590-247">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="4f590-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="4f590-248">Microsoft SQL Server テンポラル テーブル クエリをサポートするための Entity Framework Core 3.1 の LINQ 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="4f590-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="4f590-249">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-249">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-250">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="4f590-251">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="4f590-251">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="4f590-252">SQL Server EF Core プロバイダーに hierarchyid のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4f590-252">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="4f590-253">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="4f590-253">For EF Core: 3.</span></span>

[<span data-ttu-id="4f590-254">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="4f590-254">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)
