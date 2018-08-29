---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 7/3/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e9f9a6cbbceeb0379ddb5588b564b0d2a962795f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995514"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="60386-102">EF Core のツールと拡張機能</span><span class="sxs-lookup"><span data-stu-id="60386-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="60386-103">ツールと拡張機能は、Entity Framework Core の追加機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="60386-103">Tools and extensions provide additional functionality for Entity Framework Core.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="60386-104">拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="60386-104">Extensions are built by a variety of sources and not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="60386-105">サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="60386-105">When considering a third party extension, be sure to evaluate quality, licensing, compatibility, support, etc. to ensure they meet your requirements.</span></span>

## <a name="tools"></a><span data-ttu-id="60386-106">ツール</span><span class="sxs-lookup"><span data-stu-id="60386-106">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="60386-107">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="60386-107">LLBLGen Pro</span></span>

<span data-ttu-id="60386-108">LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="60386-108">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="60386-109">これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。</span><span class="sxs-lookup"><span data-stu-id="60386-109">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span>

[<span data-ttu-id="60386-110">Web サイト</span><span class="sxs-lookup"><span data-stu-id="60386-110">website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="60386-111">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="60386-111">Devart Entity Developer</span></span>

<span data-ttu-id="60386-112">Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザイナーです。</span><span class="sxs-lookup"><span data-stu-id="60386-112">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="60386-113">Model-First および Database-First アプローチを使って ORM モデルを設計し、C# または Visual Basic .NET のコードを生成することができます。</span><span class="sxs-lookup"><span data-stu-id="60386-113">You can use  Model-First and Database-First approaches to design your ORM model and generate C# or Visual Basic .NET code for it.</span></span> <span data-ttu-id="60386-114">ORM モデルをデザインするための新しいアプローチが導入されているため、生産性が向上し、データベース アプリケーションの開発が容易になります。</span><span class="sxs-lookup"><span data-stu-id="60386-114">It introduces new approaches for designing ORM models, boosts productivity, and facilitates the development of database applications.</span></span>

[<span data-ttu-id="60386-115">Web サイト</span><span class="sxs-lookup"><span data-stu-id="60386-115">website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="60386-116">EF Core のパワー ツール</span><span class="sxs-lookup"><span data-stu-id="60386-116">EF Core Power Tools</span></span>

<span data-ttu-id="60386-117">Visual Studio 2017+ 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="60386-117">Visual Studio 2017+ extension.</span></span> <span data-ttu-id="60386-118">既存のデータベースまたは SQL Server データベース プロジェクトから DbContext と POCO クラスをリバース エンジニアリングして、さまざまな方法で DbContext を視覚化し、調査することができます。</span><span class="sxs-lookup"><span data-stu-id="60386-118">You can reverse engineer of DbContext and POCO classes from an existing database or SQL Server Database project, and visualize and inspect your DbContext in various ways.</span></span>

[<span data-ttu-id="60386-119">GitHub の Wiki</span><span class="sxs-lookup"><span data-stu-id="60386-119">GitHub wiki</span></span>](https://github.com/ErikEJ/SqlCeToolbox/wiki/EF-Core-Power-Tools)

## <a name="extensions"></a><span data-ttu-id="60386-120">拡張機能</span><span class="sxs-lookup"><span data-stu-id="60386-120">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="60386-121">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="60386-121">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="60386-122">データの変更履歴の自動記録をサポートする、Microsoft.EntityFrameworkCore 用のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="60386-122">A plugin for Microsoft.EntityFrameworkCore to support automatically recording data changes history.</span></span>

[<span data-ttu-id="60386-123">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-123">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a><span data-ttu-id="60386-124">Microsoft.EntityFrameworkCore.DynamicLinq</span><span class="sxs-lookup"><span data-stu-id="60386-124">Microsoft.EntityFrameworkCore.DynamicLinq</span></span>

<span data-ttu-id="60386-125">非同期サポートを追加する Microsoft.EntityFrameworkCore 用の動的 LINQ 拡張機能</span><span class="sxs-lookup"><span data-stu-id="60386-125">Dynamic Linq extensions for Microsoft.EntityFrameworkCore which adds Async support</span></span>

 [<span data-ttu-id="60386-126">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-126">GitHub repository</span></span>](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efcorepractices"></a><span data-ttu-id="60386-127">EFCore.Practices</span><span class="sxs-lookup"><span data-stu-id="60386-127">EFCore.Practices</span></span>

<span data-ttu-id="60386-128">N+1 クエリをスキャンする小規模なフレームワークなど、API のテストをサポートする優れたプラクティスの一部が取得されます。</span><span class="sxs-lookup"><span data-stu-id="60386-128">Attempt to capture some good or best practices in an API that supports testing – including a small framework to scan for N+1 queries.</span></span>

[<span data-ttu-id="60386-129">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-129">GitHub repository</span></span>](https://github.com/riezebosch/efcore-practices/tree/master/src/EFCore.Practices/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="60386-130">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="60386-130">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="60386-131">第 2 レベルのキャッシュ ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="60386-131">Second Level Caching Library.</span></span> <span data-ttu-id="60386-132">第 2 レベルのキャッシュは、クエリ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="60386-132">Second level caching is a query cache.</span></span> <span data-ttu-id="60386-133">EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。</span><span class="sxs-lookup"><span data-stu-id="60386-133">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span>

[<span data-ttu-id="60386-134">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-134">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="detachedentityframework"></a><span data-ttu-id="60386-135">Detached.EntityFramework</span><span class="sxs-lookup"><span data-stu-id="60386-135">Detached.EntityFramework</span></span>

<span data-ttu-id="60386-136">デタッチされたエンティティのグラフ (子エンティティとリストを含むエンティティ) 全体を読み込んで、保存します。</span><span class="sxs-lookup"><span data-stu-id="60386-136">Loads and saves entire detached entity graphs (the entity with their child entities and lists).</span></span> <span data-ttu-id="60386-137">[GraphDiff](https://github.com/refactorthis/GraphDiff/) からインスパイアされています。</span><span class="sxs-lookup"><span data-stu-id="60386-137">Inspired by [GraphDiff](https://github.com/refactorthis/GraphDiff/).</span></span> <span data-ttu-id="60386-138">監査や改ページといった一部の反復的なタスクを簡略化するプラグインもいくつか追加されます。</span><span class="sxs-lookup"><span data-stu-id="60386-138">The idea is also add some plugins to simplificate some repetitive tasks, like auditing and pagination.</span></span>

[<span data-ttu-id="60386-139">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-139">GitHub repository</span></span>](https://github.com/leonardoporro/Detached/)

### <a name="entityframeworkcoreprimarykey"></a><span data-ttu-id="60386-140">EntityFrameworkCore.PrimaryKey</span><span class="sxs-lookup"><span data-stu-id="60386-140">EntityFrameworkCore.PrimaryKey</span></span>

<span data-ttu-id="60386-141">すべてのエンティティから (複合キーを含む) 主キーをディクショナリとして取得します。</span><span class="sxs-lookup"><span data-stu-id="60386-141">Retrieve the primary key (including composite keys) from any entity as a dictionary.</span></span>

[<span data-ttu-id="60386-142">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-142">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcorerx"></a><span data-ttu-id="60386-143">EntityFrameworkCore.Rx</span><span class="sxs-lookup"><span data-stu-id="60386-143">EntityFrameworkCore.Rx</span></span>

<span data-ttu-id="60386-144">Entity Framework エンティティの Hot Observable の事後対応型拡張機能ラッパー。</span><span class="sxs-lookup"><span data-stu-id="60386-144">Reactive extension wrappers for hot observables of Entity Framework entities.</span></span>

[<span data-ttu-id="60386-145">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-145">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Rx/)

### <a name="entityframeworkcoretriggers"></a><span data-ttu-id="60386-146">EntityFrameworkCore.Triggers</span><span class="sxs-lookup"><span data-stu-id="60386-146">EntityFrameworkCore.Triggers</span></span>

<span data-ttu-id="60386-147">挿入、更新、削除イベントを含むエンティティにトリガーを追加します。</span><span class="sxs-lookup"><span data-stu-id="60386-147">Add triggers to your entities with insert, update, and delete events.</span></span> <span data-ttu-id="60386-148">それぞれに対して、前、後、障害発生時の 3 つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="60386-148">There are three events for each: before, after, and upon failure.</span></span>

[<span data-ttu-id="60386-149">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-149">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.Triggers/)

### <a name="entityframeworkcoretypedoriginalvalues"></a><span data-ttu-id="60386-150">EntityFrameworkCore.TypedOriginalValues</span><span class="sxs-lookup"><span data-stu-id="60386-150">EntityFrameworkCore.TypedOriginalValues</span></span>

<span data-ttu-id="60386-151">エンティティのプロパティの OriginalValue に型指定されたアクセスができるようになります。</span><span class="sxs-lookup"><span data-stu-id="60386-151">Get typed access to the OriginalValue of your entity properties.</span></span> <span data-ttu-id="60386-152">単純または複合プロパティがサポートされますが、ナビゲーション/コレクションはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="60386-152">Simple and complex properties are supported, navigation/collections are not.</span></span>

[<span data-ttu-id="60386-153">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-153">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a><span data-ttu-id="60386-154">Geco</span><span class="sxs-lookup"><span data-stu-id="60386-154">Geco</span></span>

<span data-ttu-id="60386-155">Geco は複数形化/単数形化のサポートと、C# 6.0 の挿入文字列に基づき .Net Core で実行される編集可能なテンプレートを含むリバース モデル ジェネレーターを提供します。</span><span class="sxs-lookup"><span data-stu-id="60386-155">Geco provides a Reverse Model generator with support for Pluralization/Singularization and editable templates based on C# 6.0 interpolated strings and running on .Net Core.</span></span> <span data-ttu-id="60386-156">また、SQL マージ スクリプトとスクリプト ランナーを含むシード スクリプト ジェネレーターも提供します。</span><span class="sxs-lookup"><span data-stu-id="60386-156">It also provides an Seed script generator with SQL Merge scripts and an script runner.</span></span>

[<span data-ttu-id="60386-157">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-157">Github repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a><span data-ttu-id="60386-158">LinqKit.Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="60386-158">LinqKit.Microsoft.EntityFrameworkCore</span></span>

<span data-ttu-id="60386-159">LinqKit.Microsoft.EntityFrameworkCore は、LINQ to SQL と EntityFrameworkCore のパワー ユーザー向けの無料の拡張機能セットです。</span><span class="sxs-lookup"><span data-stu-id="60386-159">LinqKit.Microsoft.EntityFrameworkCore is a free set of extensions for LINQ to SQL and EntityFrameworkCore power users.</span></span> <span data-ttu-id="60386-160">Include(...) と IDbAsync をサポートします。</span><span class="sxs-lookup"><span data-stu-id="60386-160">With Include(...) and IDbAsync support.</span></span>

[<span data-ttu-id="60386-161">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-161">GitHub repository</span></span>](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="60386-162">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="60386-162">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="60386-163">NeinLinq.EntityFrameworkCore は、.NET 関数の一部のサブセットのみをサポートするエンティティ フレームワークなどの LINQ プロバイダーの使用、関数の再利用、クエリの再記述と null セーフ設定、翻訳可能な述語とセレクターを使った動的クエリの構築に便利な拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="60386-163">NeinLinq.EntityFrameworkCore provides helpful extensions for using LINQ providers such as Entity Framework that support only a minor subset of .NET functions, reusing functions, rewriting queries, even making them null-safe, and building dynamic queries using translatable predicates and selectors.</span></span>

[<span data-ttu-id="60386-164">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-164">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="60386-165">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="60386-165">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="60386-166">リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。</span><span class="sxs-lookup"><span data-stu-id="60386-166">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple database with distributed transaction supported.</span></span>

[<span data-ttu-id="60386-167">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-167">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="entityframeworklazyloading"></a><span data-ttu-id="60386-168">EntityFramework.LazyLoading</span><span class="sxs-lookup"><span data-stu-id="60386-168">EntityFramework.LazyLoading</span></span>

<span data-ttu-id="60386-169">EF Core 1.1 の遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="60386-169">Lazy Loading for EF Core 1.1</span></span>

[<span data-ttu-id="60386-170">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-170">GitHub repository</span></span>](https://github.com/darxis/EntityFramework.LazyLoading)

### <a name="efcorebulkextensions"></a><span data-ttu-id="60386-171">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="60386-171">EFCore.BulkExtensions</span></span>

<span data-ttu-id="60386-172">一括操作 (挿入、更新、削除) の EntityFrameworkCore 拡張機能。</span><span class="sxs-lookup"><span data-stu-id="60386-172">EntityFrameworkCore extensions for Bulk operations (Insert, Update, Delete).</span></span>

[<span data-ttu-id="60386-173">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-173">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="60386-174">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="60386-174">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="60386-175">EF Core へのデザイン時の複数形化の追加。</span><span class="sxs-lookup"><span data-stu-id="60386-175">Adds design-time pluralization to EF Core.</span></span>

[<span data-ttu-id="60386-176">GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="60386-176">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)
