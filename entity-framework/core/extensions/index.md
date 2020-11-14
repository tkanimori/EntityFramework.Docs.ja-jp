---
title: ツールと拡張機能 - EF Core
description: Entity Framework Core の外部ツールと拡張機能
author: ErikEJ
ms.date: 04/11/2020
uid: core/extensions/index
ms.openlocfilehash: c7056bcb0831ae1919b3060aacf73dc5cb9c8cb1
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429937"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="2833f-103">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="2833f-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="2833f-104">以下のツールと拡張機能は、Entity Framework Core 2.1 およびそれ以降の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="2833f-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="2833f-105">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="2833f-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="2833f-106">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="2833f-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="2833f-107">特に、古いバージョンの EF Core 用に構築された拡張機能は、最新バージョンで動作させる前に更新が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2833f-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="2833f-108">ツール</span><span class="sxs-lookup"><span data-stu-id="2833f-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="2833f-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="2833f-109">LLBLGen Pro</span></span>

<span data-ttu-id="2833f-110">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="2833f-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="2833f-111">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="2833f-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="2833f-112">対象の EF Core:2、3</span><span class="sxs-lookup"><span data-stu-id="2833f-112">For EF Core: 2, 3</span></span>

[<span data-ttu-id="2833f-113">Web サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="2833f-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="2833f-114">Devart Entity Developer</span></span>

<span data-ttu-id="2833f-115">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な O/RM デザイナーです。</span><span class="sxs-lookup"><span data-stu-id="2833f-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="2833f-116">モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="2833f-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="2833f-117">対象の EF Core:1、2、3、5。</span><span class="sxs-lookup"><span data-stu-id="2833f-117">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="2833f-118">Web サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="2833f-119">Entity Framework 用の nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="2833f-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="2833f-120">厳密に型指定された、Entity Framework 用に拡張できるクラスを作成する O/RM です。</span><span class="sxs-lookup"><span data-stu-id="2833f-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="2833f-121">生成されたコードは Entity Framework Core になります。</span><span class="sxs-lookup"><span data-stu-id="2833f-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="2833f-122">これらに違いはありません。</span><span class="sxs-lookup"><span data-stu-id="2833f-122">There is no difference.</span></span> <span data-ttu-id="2833f-123">これは、EF またはカスタム O/RM に代わるものではありません。</span><span class="sxs-lookup"><span data-stu-id="2833f-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="2833f-124">チームが複雑なデータベース スキーマを管理できるビジュアルのモデリング レイヤーです。</span><span class="sxs-lookup"><span data-stu-id="2833f-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="2833f-125">これは、Git のような SCM ソフトウェアに適しており、最小限の競合でご利用のモデルへのマルチ ユーザー アクセスが可能になります。</span><span class="sxs-lookup"><span data-stu-id="2833f-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="2833f-126">インストーラーによって、モデルの変更が追跡され、アップグレード スクリプトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2833f-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="2833f-127">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-127">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-128">GitHub サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-128">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="2833f-129">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="2833f-129">EF Core Power Tools</span></span>

<span data-ttu-id="2833f-130">EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="2833f-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="2833f-131">既存のデータベースと [SQL Server DACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2833f-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="2833f-132">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-132">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-133">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="2833f-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="2833f-134">Entity Framework のビジュアル エディター</span><span class="sxs-lookup"><span data-stu-id="2833f-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="2833f-135">Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの O/RM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="2833f-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="2833f-136">コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="2833f-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="2833f-137">継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2833f-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="2833f-138">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-138">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="2833f-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="2833f-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="2833f-140">CatFactory</span></span>

<span data-ttu-id="2833f-141">CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。</span><span class="sxs-lookup"><span data-stu-id="2833f-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="2833f-142">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-142">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-143">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="2833f-144">LoreSoft の Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="2833f-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="2833f-145">Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2833f-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="2833f-146">このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2833f-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="2833f-147">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-147">For EF Core: 2.</span></span>

<span data-ttu-id="2833f-148">[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="2833f-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="2833f-149">拡張機能</span><span class="sxs-lookup"><span data-stu-id="2833f-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="2833f-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="2833f-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="2833f-151">EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="2833f-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="2833f-152">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-152">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-153">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="2833f-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="2833f-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="2833f-155">第 2 レベルのキャッシュは、クエリ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="2833f-155">Second level caching is a query cache.</span></span> <span data-ttu-id="2833f-156">EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。</span><span class="sxs-lookup"><span data-stu-id="2833f-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="2833f-157">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-157">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-158">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="2833f-159">Geco</span><span class="sxs-lookup"><span data-stu-id="2833f-159">Geco</span></span>

<span data-ttu-id="2833f-160">Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="2833f-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="2833f-161">Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2833f-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="2833f-162">シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。</span><span class="sxs-lookup"><span data-stu-id="2833f-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="2833f-163">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-163">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-164">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="2833f-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="2833f-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="2833f-166">Entity Framework Core ツールチェーンと Handlebars テンプレートを使用して、既存のデータベースからリバース エンジニアリングされたクラスのカスタマイズを可能にします。</span><span class="sxs-lookup"><span data-stu-id="2833f-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="2833f-167">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-167">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-168">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="2833f-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2833f-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="2833f-170">NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。</span><span class="sxs-lookup"><span data-stu-id="2833f-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="2833f-171">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-171">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-172">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="2833f-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="2833f-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="2833f-174">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="2833f-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="2833f-175">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-175">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-176">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="2833f-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="2833f-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="2833f-178">一括操作 (挿入、更新、削除) の EF Core 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="2833f-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="2833f-179">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-180">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="2833f-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="2833f-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="2833f-182">デザイン時の複数形化を追加します。</span><span class="sxs-lookup"><span data-stu-id="2833f-182">Adds design-time pluralization.</span></span> <span data-ttu-id="2833f-183">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-183">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-184">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="2833f-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="2833f-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="2833f-186">[Index] 属性のリバイバルです (モデル構築のための拡張機能を含む)。</span><span class="sxs-lookup"><span data-stu-id="2833f-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="2833f-187">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-187">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-188">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="2833f-189">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="2833f-189">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="2833f-190">EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。</span><span class="sxs-lookup"><span data-stu-id="2833f-190">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="2833f-191">リレーショナル プロバイダーのような動作になります。</span><span class="sxs-lookup"><span data-stu-id="2833f-191">Makes it act more like a relational provider.</span></span> <span data-ttu-id="2833f-192">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-192">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-193">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-193">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="2833f-194">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="2833f-194">EFCore.TemporalSupport</span></span>

<span data-ttu-id="2833f-195">テンポラル サポートの実装。</span><span class="sxs-lookup"><span data-stu-id="2833f-195">An implementation of temporal support.</span></span> <span data-ttu-id="2833f-196">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-196">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-197">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-197">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="2833f-198">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="2833f-198">EfCoreTemporalTable</span></span>

<span data-ttu-id="2833f-199">導入された拡張メソッド `AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)` を使用して、任意のデータベース上で簡単にテンポラル クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2833f-199">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="2833f-200">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-200">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-201">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-201">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="2833f-202">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="2833f-202">EFCore.TimeTraveler</span></span>

<span data-ttu-id="2833f-203">EF Core コード、エンティティ、および既に定義済みのマッピングを使用して、[SQL Server テンポラル履歴](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)に対するフル機能の Entity Framework Core クエリを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="2833f-203">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="2833f-204">コードを `using (TemporalQuery.AsOf(targetDateTime)) {...}` でラップすることにより、時間に関係なく使用できます。</span><span class="sxs-lookup"><span data-stu-id="2833f-204">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="2833f-205">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-205">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-206">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-206">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="2833f-207">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="2833f-207">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="2833f-208">SQL Server を使用している開発者がテンポラル テーブルを簡単に使用できるようにする、Entity Framework Core の拡張ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="2833f-208">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="2833f-209">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-209">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-210">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-210">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="2833f-211">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="2833f-211">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="2833f-212">高パフォーマンスな第 2 レベルのクエリ キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="2833f-212">A high-performance second-level query cache.</span></span> <span data-ttu-id="2833f-213">対象の EF Core:2.</span><span class="sxs-lookup"><span data-stu-id="2833f-213">For EF Core: 2.</span></span>

[<span data-ttu-id="2833f-214">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-214">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="2833f-215">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="2833f-215">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="2833f-216">NCache Entity Framework Core Provider は、クエリの結果をキャッシュする、分散された第 2 レベルのキャッシュ プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="2833f-216">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="2833f-217">NCache の分散アーキテクチャにより、拡張性と可用性が向上しています。</span><span class="sxs-lookup"><span data-stu-id="2833f-217">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="2833f-218">対象は EF Core 2 です。</span><span class="sxs-lookup"><span data-stu-id="2833f-218">For EF Core 2.</span></span>

[<span data-ttu-id="2833f-219">Web サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-219">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="2833f-220">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="2833f-220">Entity Framework Plus</span></span>

<span data-ttu-id="2833f-221">次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。</span><span class="sxs-lookup"><span data-stu-id="2833f-221">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="2833f-222">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-222">For EF Core: 2, 3.</span></span>

<span data-ttu-id="2833f-223">[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="2833f-223">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="2833f-224">Entity Framework Extensions</span><span class="sxs-lookup"><span data-stu-id="2833f-224">Entity Framework Extensions</span></span>

<span data-ttu-id="2833f-225">高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。</span><span class="sxs-lookup"><span data-stu-id="2833f-225">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="2833f-226">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-226">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-227">Web サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-227">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="2833f-228">Expressionify</span><span class="sxs-lookup"><span data-stu-id="2833f-228">Expressionify</span></span>

<span data-ttu-id="2833f-229">LINQ ラムダ式で拡張メソッドを呼び出すためのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="2833f-229">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="2833f-230">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-230">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-231">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-231">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="2833f-232">XLinq</span><span class="sxs-lookup"><span data-stu-id="2833f-232">XLinq</span></span>

<span data-ttu-id="2833f-233">リレーショナル データベース用の統合言語クエリ (LINQ) テクノロジ。</span><span class="sxs-lookup"><span data-stu-id="2833f-233">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="2833f-234">C# を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="2833f-234">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="2833f-235">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-235">For EF Core: 3.</span></span>

- <span data-ttu-id="2833f-236">クエリの作成時のラムダ、変数、関数内での複数のステートメントなどの C# の完全なサポート。</span><span class="sxs-lookup"><span data-stu-id="2833f-236">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="2833f-237">SQL と意味的な違いはありません。</span><span class="sxs-lookup"><span data-stu-id="2833f-237">No semantic gap with SQL.</span></span> <span data-ttu-id="2833f-238">XLinq では SQL ステートメント (`SELECT`、`FROM`、`WHERE`) を第一級の C# メソッドとして、使い慣れた構文を intellisense、タイプ セーフおよびリファクタリングと組み合わせ宣言します。</span><span class="sxs-lookup"><span data-stu-id="2833f-238">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="2833f-239">その結果、SQL はその API をローカルに公開する、文字どおりの *"統合言語 SQL"* である "もう 1 つの" クラス ライブラリになります。</span><span class="sxs-lookup"><span data-stu-id="2833f-239">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="2833f-240">Web サイト</span><span class="sxs-lookup"><span data-stu-id="2833f-240">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="2833f-241">Ramses</span><span class="sxs-lookup"><span data-stu-id="2833f-241">Ramses</span></span>

<span data-ttu-id="2833f-242">ライフサイクル フック (SaveChanges 用)。</span><span class="sxs-lookup"><span data-stu-id="2833f-242">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="2833f-243">対象の EF Core:2、3。</span><span class="sxs-lookup"><span data-stu-id="2833f-243">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2833f-244">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-244">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="2833f-245">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="2833f-245">EFCore.NamingConventions</span></span>

<span data-ttu-id="2833f-246">これにより、snake_case を含むすべてのテーブル名と列名が自動的にすべて大文字またはすべて小文字の名前になります。</span><span class="sxs-lookup"><span data-stu-id="2833f-246">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="2833f-247">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-247">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-248">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-248">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="2833f-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="2833f-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="2833f-250">NodaTime の SQL Server 用 EntityFrameworkCore にネイティブ サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="2833f-250">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="2833f-251">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-251">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-252">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-252">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="2833f-253">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="2833f-253">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="2833f-254">Microsoft SQL Server テンポラル テーブル クエリをサポートするための Entity Framework Core 3.1 の LINQ 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="2833f-254">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="2833f-255">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-255">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-256">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-256">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="2833f-257">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="2833f-257">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="2833f-258">SQL Server EF Core プロバイダーに hierarchyid のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="2833f-258">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="2833f-259">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-259">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-260">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-260">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="2833f-261">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2833f-261">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="2833f-262">SQL 式に対する LINQ クエリの代替トランスレーター。</span><span class="sxs-lookup"><span data-stu-id="2833f-262">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="2833f-263">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-263">For EF Core: 3.</span></span>

<span data-ttu-id="2833f-264">CTE、一括コピー、テーブル ヒント、ウィンドウ関数、一時テーブル、データベース側の作成、更新、削除作などの高度な SQL 機能のサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2833f-264">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="2833f-265">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-265">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="2833f-266">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="2833f-266">EFCore.SoftDelete</span></span>

<span data-ttu-id="2833f-267">エンティティの論理的な削除のための実装。</span><span class="sxs-lookup"><span data-stu-id="2833f-267">An implementation for soft deleting entities.</span></span> <span data-ttu-id="2833f-268">対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-268">For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-269">NuGet</span><span class="sxs-lookup"><span data-stu-id="2833f-269">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="2833f-270">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="2833f-270">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="2833f-271">EF Core を拡張して、App.config からの接続文字列を解決します。対象の EF Core:3.</span><span class="sxs-lookup"><span data-stu-id="2833f-271">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="2833f-272">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="2833f-272">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)
