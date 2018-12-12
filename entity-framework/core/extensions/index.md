---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 07/03/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 67eae6cb943b974cc9cd581b8054836d2e37b1e9
ms.sourcegitcommit: a6082a2caee62029f101eb1000656966195cd6ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53181995"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="48f4b-102">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="48f4b-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="48f4b-103">ツールと拡張機能は、Entity Framework Core の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-103">Tools and extensions provide additional functionality for Entity Framework Core.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="48f4b-104">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="48f4b-104">Extensions are built by a variety of sources and not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="48f4b-105">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="48f4b-105">When considering a third party extension, be sure to evaluate quality, licensing, compatibility, support, etc. to ensure they meet your requirements.</span></span>

## <a name="tools"></a><span data-ttu-id="48f4b-106">ツール</span><span class="sxs-lookup"><span data-stu-id="48f4b-106">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="48f4b-107">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="48f4b-107">LLBLGen Pro</span></span>

<span data-ttu-id="48f4b-108">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="48f4b-108">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="48f4b-109">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-109">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span>

[<span data-ttu-id="48f4b-110">Web サイト</span><span class="sxs-lookup"><span data-stu-id="48f4b-110">website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="48f4b-111">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="48f4b-111">Devart Entity Developer</span></span>

<span data-ttu-id="48f4b-112">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザイナーです。</span><span class="sxs-lookup"><span data-stu-id="48f4b-112">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="48f4b-113">Model-First および Database-First アプローチを使って ORM モデルを設計し、C# または Visual Basic .NET のコードを生成することができます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-113">You can use  Model-First and Database-First approaches to design your ORM model and generate C# or Visual Basic .NET code for it.</span></span> <span data-ttu-id="48f4b-114">ORM モデルをデザインするための新しいアプローチが導入されているため、生産性が向上し、データベース アプリケーションの開発が容易になります。</span><span class="sxs-lookup"><span data-stu-id="48f4b-114">It introduces new approaches for designing ORM models, boosts productivity, and facilitates the development of database applications.</span></span>

[<span data-ttu-id="48f4b-115">Web サイト</span><span class="sxs-lookup"><span data-stu-id="48f4b-115">website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="48f4b-116">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="48f4b-116">EF Core Power Tools</span></span>

<span data-ttu-id="48f4b-117">Visual Studio 2017+ 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="48f4b-117">Visual Studio 2017+ extension.</span></span> <span data-ttu-id="48f4b-118">既存のデータベースまたは SQL Server データベース プロジェクトから DbContext と POCO クラスをリバース エンジニアリングして、さまざまな方法で DbContext を視覚化し、調査することができます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-118">You can reverse engineer of DbContext and POCO classes from an existing database or SQL Server Database project, and visualize and inspect your DbContext in various ways.</span></span>

[<span data-ttu-id="48f4b-119">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="48f4b-119">GitHub wiki</span></span>](https://github.com/ErikEJ/SqlCeToolbox/wiki/EF-Core-Power-Tools)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="48f4b-120">Entity Framework のビジュアル エディター</span><span class="sxs-lookup"><span data-stu-id="48f4b-120">Entity Framework Visual Editor</span></span>

<span data-ttu-id="48f4b-121">Entity Framework 6、Core 2.0 および Core 2.1 クラスのビジュアル デ ザインの ORM デザイナーに追加する Visual Studio 2017 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="48f4b-121">A Visual Studio 2017 extension that adds an ORM designer for visual design of Entity Framework 6, Core 2.0 and Core 2.1 classes.</span></span> <span data-ttu-id="48f4b-122">T4 テンプレートを使用して生成されたコードは、すべてのニーズに合わせて完全にカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-122">Code is generated using T4 templates so can be completely customized to suit any needs.</span></span> <span data-ttu-id="48f4b-123">継承、一方向および双方向の関連付けがすべてサポートされ、列挙体やクラスを色分けし、潜在的な設計の難解な部分を説明するテキストのブロックを追加する機能。</span><span class="sxs-lookup"><span data-stu-id="48f4b-123">Inheritance, unidirectional and bidirectional associations are all supported, as are enumerations and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span>

[<span data-ttu-id="48f4b-124">Marketplace</span><span class="sxs-lookup"><span data-stu-id="48f4b-124">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="48f4b-125">CatFactory</span><span class="sxs-lookup"><span data-stu-id="48f4b-125">CatFactory</span></span>

<span data-ttu-id="48f4b-126">CatFactory は、.NET Core と Entity Framework Core のためのスキャフォールディング エンジンです。</span><span class="sxs-lookup"><span data-stu-id="48f4b-126">CatFactory is a scaffolding engine for .NET Core and Entity Framework Core.</span></span> <span data-ttu-id="48f4b-127">CatFactory の背後にある概念は、SQL Server インスタンスからの既存のデータベースのエクスポートであり、モデル内でのそのデータベースの表現を使用してエンティティ、構成、リポジトリなどのスキャフォールディングを行います。</span><span class="sxs-lookup"><span data-stu-id="48f4b-127">The concept behind CatFactory it's export an existing database from SQL Server instance, then with the representation in models for database; scaffold entities, configurations, repositories and more.</span></span>

[<span data-ttu-id="48f4b-128">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-128">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="48f4b-129">LoreSoft の Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="48f4b-129">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="48f4b-130">Entity Framework Core Generator (efg) は .NET Core CLI ツールの 1 つです。EF Core のモデルを既存のデータベースから生成でき、`dotnet ef dbcontext scaffold` によく似ています。</span><span class="sxs-lookup"><span data-stu-id="48f4b-130">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="48f4b-131">ただし、セーフ コード[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしている点が異なります。</span><span class="sxs-lookup"><span data-stu-id="48f4b-131">However it's different in that it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/).</span></span> <span data-ttu-id="48f4b-132">再生成は、リージョン置換によって、またはマッピング ファイルを解析することによって達成されます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-132">Regeneration is accomplished either via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="48f4b-133">このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="48f4b-133">The tool also supports generating view models, validation and object mapper code.</span></span> <span data-ttu-id="48f4b-134">詳細については、このチュートリアルと製品ドキュメントのリンクをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="48f4b-134">For more information, see the tutorial and the product documentation links.</span></span>

<span data-ttu-id="48f4b-135">[チュートリアル](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="48f4b-135">[Tutorial](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="48f4b-136">拡張機能</span><span class="sxs-lookup"><span data-stu-id="48f4b-136">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="48f4b-137">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="48f4b-137">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="48f4b-138">データの変更履歴の自動記録をサポートする、Microsoft.EntityFrameworkCore 用のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="48f4b-138">A plugin for Microsoft.EntityFrameworkCore to support automatically recording data changes history.</span></span>

[<span data-ttu-id="48f4b-139">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-139">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a><span data-ttu-id="48f4b-140">Microsoft.EntityFrameworkCore.DynamicLinq</span><span class="sxs-lookup"><span data-stu-id="48f4b-140">Microsoft.EntityFrameworkCore.DynamicLinq</span></span>

<span data-ttu-id="48f4b-141">非同期サポートを追加する Microsoft.EntityFrameworkCore 用の動的 LINQ 拡張機能</span><span class="sxs-lookup"><span data-stu-id="48f4b-141">Dynamic Linq extensions for Microsoft.EntityFrameworkCore which adds Async support</span></span>

 [<span data-ttu-id="48f4b-142">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-142">GitHub repository</span></span>](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efcorepractices"></a><span data-ttu-id="48f4b-143">EFCore.Practices</span><span class="sxs-lookup"><span data-stu-id="48f4b-143">EFCore.Practices</span></span>

<span data-ttu-id="48f4b-144">N+1 クエリをスキャンする小規模なフレームワークなど、API のテストをサポートする優れたプラクティスの一部が取得されます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-144">Attempt to capture some good or best practices in an API that supports testing – including a small framework to scan for N+1 queries.</span></span>

[<span data-ttu-id="48f4b-145">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-145">GitHub repository</span></span>](https://github.com/riezebosch/efcore-practices/tree/master/src/EFCore.Practices/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="48f4b-146">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="48f4b-146">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="48f4b-147">第 2 レベルのキャッシュ ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="48f4b-147">Second Level Caching Library.</span></span> <span data-ttu-id="48f4b-148">第 2 レベルのキャッシュは、クエリ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="48f4b-148">Second level caching is a query cache.</span></span> <span data-ttu-id="48f4b-149">EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。</span><span class="sxs-lookup"><span data-stu-id="48f4b-149">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span>

[<span data-ttu-id="48f4b-150">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-150">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="detachedentityframework"></a><span data-ttu-id="48f4b-151">Detached.EntityFramework</span><span class="sxs-lookup"><span data-stu-id="48f4b-151">Detached.EntityFramework</span></span>

<span data-ttu-id="48f4b-152">デタッチされたエンティティのグラフ (子エンティティとリストを含むエンティティ) 全体を読み込んで、保存します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-152">Loads and saves entire detached entity graphs (the entity with their child entities and lists).</span></span> <span data-ttu-id="48f4b-153">[GraphDiff](https://github.com/refactorthis/GraphDiff/) からインスパイアされています。</span><span class="sxs-lookup"><span data-stu-id="48f4b-153">Inspired by [GraphDiff](https://github.com/refactorthis/GraphDiff/).</span></span> <span data-ttu-id="48f4b-154">監査や改ページといった一部の反復的なタスクを簡略化するプラグインもいくつか追加されます。</span><span class="sxs-lookup"><span data-stu-id="48f4b-154">The idea is also add some plugins to simplificate some repetitive tasks, like auditing and pagination.</span></span>

[<span data-ttu-id="48f4b-155">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-155">GitHub repository</span></span>](https://github.com/leonardoporro/Detached/)

### <a name="entityframeworkcoreprimarykey"></a><span data-ttu-id="48f4b-156">EntityFrameworkCore.PrimaryKey</span><span class="sxs-lookup"><span data-stu-id="48f4b-156">EntityFrameworkCore.PrimaryKey</span></span>

<span data-ttu-id="48f4b-157">すべてのエンティティから (複合キーを含む) 主キーをディクショナリとして取得します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-157">Retrieve the primary key (including composite keys) from any entity as a dictionary.</span></span>

[<span data-ttu-id="48f4b-158">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-158">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcorerx"></a><span data-ttu-id="48f4b-159">EntityFrameworkCore.Rx</span><span class="sxs-lookup"><span data-stu-id="48f4b-159">EntityFrameworkCore.Rx</span></span>

<span data-ttu-id="48f4b-160">Entity Framework エンティティの Hot Observable の事後対応型拡張機能ラッパー。</span><span class="sxs-lookup"><span data-stu-id="48f4b-160">Reactive extension wrappers for hot observables of Entity Framework entities.</span></span>

[<span data-ttu-id="48f4b-161">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-161">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Rx/)

### <a name="entityframeworkcoretriggers"></a><span data-ttu-id="48f4b-162">EntityFrameworkCore.Triggers</span><span class="sxs-lookup"><span data-stu-id="48f4b-162">EntityFrameworkCore.Triggers</span></span>

<span data-ttu-id="48f4b-163">挿入、更新、削除イベントを含むエンティティにトリガーを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-163">Add triggers to your entities with insert, update, and delete events.</span></span> <span data-ttu-id="48f4b-164">それぞれに対して、前、後、障害発生時の 3 つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="48f4b-164">There are three events for each: before, after, and upon failure.</span></span>

[<span data-ttu-id="48f4b-165">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-165">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Triggers/)

### <a name="entityframeworkcoretypedoriginalvalues"></a><span data-ttu-id="48f4b-166">EntityFrameworkCore.TypedOriginalValues</span><span class="sxs-lookup"><span data-stu-id="48f4b-166">EntityFrameworkCore.TypedOriginalValues</span></span>

<span data-ttu-id="48f4b-167">エンティティのプロパティの OriginalValue に型指定されたアクセスができるようになります。</span><span class="sxs-lookup"><span data-stu-id="48f4b-167">Get typed access to the OriginalValue of your entity properties.</span></span> <span data-ttu-id="48f4b-168">単純または複合プロパティがサポートされますが、ナビゲーション/コレクションはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="48f4b-168">Simple and complex properties are supported, navigation/collections are not.</span></span>

[<span data-ttu-id="48f4b-169">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-169">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a><span data-ttu-id="48f4b-170">Geco</span><span class="sxs-lookup"><span data-stu-id="48f4b-170">Geco</span></span>

<span data-ttu-id="48f4b-171">Geco は複数形化/単数形化のサポートと、C# 6.0 の挿入文字列に基づき .Net Core で実行される編集可能なテンプレートを含むリバース モデル ジェネレーターを提供します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-171">Geco provides a Reverse Model generator with support for Pluralization/Singularization and editable templates based on C# 6.0 interpolated strings and running on .Net Core.</span></span> <span data-ttu-id="48f4b-172">また、SQL マージ スクリプトとスクリプト ランナーを含むシード スクリプト ジェネレーターも提供します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-172">It also provides an Seed script generator with SQL Merge scripts and an script runner.</span></span>

[<span data-ttu-id="48f4b-173">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-173">Github repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a><span data-ttu-id="48f4b-174">LinqKit.Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="48f4b-174">LinqKit.Microsoft.EntityFrameworkCore</span></span>

<span data-ttu-id="48f4b-175">LinqKit.Microsoft.EntityFrameworkCore は、LINQ to SQL と EntityFrameworkCore のパワー ユーザー向けの無料の拡張機能セットです。</span><span class="sxs-lookup"><span data-stu-id="48f4b-175">LinqKit.Microsoft.EntityFrameworkCore is a free set of extensions for LINQ to SQL and EntityFrameworkCore power users.</span></span> <span data-ttu-id="48f4b-176">Include(...) と IDbAsync をサポートします。</span><span class="sxs-lookup"><span data-stu-id="48f4b-176">With Include(...) and IDbAsync support.</span></span>

[<span data-ttu-id="48f4b-177">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-177">GitHub repository</span></span>](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="48f4b-178">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="48f4b-178">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="48f4b-179">NeinLinq.EntityFrameworkCore は、.NET 関数の一部のサブセットのみをサポートするエンティティ フレームワークなどの LINQ プロバイダーの使用、関数の再利用、クエリの再記述と null セーフ設定、翻訳可能な述語とセレクターを使った動的クエリの構築に便利な拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="48f4b-179">NeinLinq.EntityFrameworkCore provides helpful extensions for using LINQ providers such as Entity Framework that support only a minor subset of .NET functions, reusing functions, rewriting queries, even making them null-safe, and building dynamic queries using translatable predicates and selectors.</span></span>

[<span data-ttu-id="48f4b-180">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-180">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="48f4b-181">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="48f4b-181">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="48f4b-182">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="48f4b-182">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple database with distributed transaction supported.</span></span>

[<span data-ttu-id="48f4b-183">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-183">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="entityframeworklazyloading"></a><span data-ttu-id="48f4b-184">EntityFramework.LazyLoading</span><span class="sxs-lookup"><span data-stu-id="48f4b-184">EntityFramework.LazyLoading</span></span>

<span data-ttu-id="48f4b-185">EF Core 1.1 の遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="48f4b-185">Lazy Loading for EF Core 1.1</span></span>

[<span data-ttu-id="48f4b-186">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-186">GitHub repository</span></span>](https://github.com/darxis/EntityFramework.LazyLoading)

### <a name="efcorebulkextensions"></a><span data-ttu-id="48f4b-187">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="48f4b-187">EFCore.BulkExtensions</span></span>

<span data-ttu-id="48f4b-188">一括操作 (挿入、更新、削除) の EntityFrameworkCore 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="48f4b-188">EntityFrameworkCore extensions for Bulk operations (Insert, Update, Delete).</span></span>

[<span data-ttu-id="48f4b-189">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-189">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="48f4b-190">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="48f4b-190">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="48f4b-191">EF Core へのデザイン時の複数形化の追加。</span><span class="sxs-lookup"><span data-stu-id="48f4b-191">Adds design-time pluralization to EF Core.</span></span>

[<span data-ttu-id="48f4b-192">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="48f4b-192">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)
