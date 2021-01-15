---
title: Entity Framework Core の概要 - EF Core
description: Entity Framework Core の一般的な概要
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: f2905fc55419e196a6f5b8883cf2a2a76b8a1ab2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128603"
---
# <a name="entity-framework-core"></a><span data-ttu-id="589f5-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="589f5-103">Entity Framework Core</span></span>

<span data-ttu-id="589f5-104">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/dotnet/efcore)で、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="589f5-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/dotnet/efcore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="589f5-105">EF Core は、次のようなオブジェクト リレーショナル マッパー (O/RM) として機能します。</span><span class="sxs-lookup"><span data-stu-id="589f5-105">EF Core can serve as an object-relational mapper (O/RM), which:</span></span>

* <span data-ttu-id="589f5-106">.NET 開発者が .NET オブジェクトを使用してデータベースを操作できるようにする。</span><span class="sxs-lookup"><span data-stu-id="589f5-106">Enables .NET developers to work with a database using .NET objects.</span></span>
* <span data-ttu-id="589f5-107">通常記述しなければならないデータアクセス コードの多くを不要にする。</span><span class="sxs-lookup"><span data-stu-id="589f5-107">Eliminates the need for most of the data-access code that typically needs to be written.</span></span>

<span data-ttu-id="589f5-108">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](xref:core/providers/index)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="589f5-108">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="589f5-109">Model</span><span class="sxs-lookup"><span data-stu-id="589f5-109">The model</span></span>

<span data-ttu-id="589f5-110">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="589f5-110">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="589f5-111">モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。</span><span class="sxs-lookup"><span data-stu-id="589f5-111">A model is made up of entity classes and a context object that represents a session with the database.</span></span> <span data-ttu-id="589f5-112">このコンテキスト オブジェクトにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="589f5-112">The context object allows querying and saving data.</span></span> <span data-ttu-id="589f5-113">詳しくは、「[モデルの作成](xref:core/modeling/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="589f5-113">For more information, see [Creating a Model](xref:core/modeling/index).</span></span>

<span data-ttu-id="589f5-114">EF は、次のモデル開発アプローチをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="589f5-114">EF supports the following model development approaches:</span></span>

* <span data-ttu-id="589f5-115">既存のデータベースからモデルを生成する。</span><span class="sxs-lookup"><span data-stu-id="589f5-115">Generate a model from an existing database.</span></span>
* <span data-ttu-id="589f5-116">データベースに合わせてモデルのコードを手動で書く。</span><span class="sxs-lookup"><span data-stu-id="589f5-116">Hand code a model to match the database.</span></span>
* <span data-ttu-id="589f5-117">モデルが作成されたら、[EF の移行](xref:core/managing-schemas/migrations/index) を使用して、モデルからデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="589f5-117">Once a model is created, use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from the model.</span></span> <span data-ttu-id="589f5-118">移行により、モデルの変更に応じてデータベースを進化させることができます。</span><span class="sxs-lookup"><span data-stu-id="589f5-118">Migrations allow evolving the database as the model changes.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="589f5-119">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="589f5-119">Querying</span></span>

<span data-ttu-id="589f5-120">エンティティ クラスのインスタンスは、[統合言語クエリ (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="589f5-120">Instances of your entity classes are retrieved from the database using [Language Integrated Query (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/).</span></span> <span data-ttu-id="589f5-121">詳しくは、「[データのクエリ](xref:core/querying/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="589f5-121">For more information, see [Querying Data](xref:core/querying/index).</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="589f5-122">データの保存</span><span class="sxs-lookup"><span data-stu-id="589f5-122">Saving data</span></span>

<span data-ttu-id="589f5-123">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="589f5-123">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="589f5-124">詳細については、「[Saving Data](xref:core/saving/index)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="589f5-124">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a><span data-ttu-id="589f5-125">EF O/RM の考慮事項</span><span class="sxs-lookup"><span data-stu-id="589f5-125">EF O/RM considerations</span></span>

<span data-ttu-id="589f5-126">EF Core は多くのプログラミングの詳細を抽象化するのに適していますが、運用アプリでよくある落とし穴を回避するために、どの O/RM にも適用できるいくつかのベスト プラクティスがあります。</span><span class="sxs-lookup"><span data-stu-id="589f5-126">While EF Core is good at abstracting many programming details, there are some best practices applicable to any O/RM that help to avoid common pitfalls in production apps:</span></span>

* <span data-ttu-id="589f5-127">高パフォーマンスの運用アプリでデータを設計、デバッグ、プロファイル、移行するには、基になるデータベース サーバーの中級レベル以上の知識が不可欠です。</span><span class="sxs-lookup"><span data-stu-id="589f5-127">Intermediate-level knowledge or higher of the underlying database server is essential to architect, debug, profile, and migrate data in high performance production apps.</span></span> <span data-ttu-id="589f5-128">たとえば、主キーと外部キー、制約、インデックス、正規化、DML および DDL のステートメント、データ型、プロファイルなどの知識です。</span><span class="sxs-lookup"><span data-stu-id="589f5-128">For example, knowledge of primary and foreign keys, constraints, indexes, normalization, DML and DDL statements, data types, profiling, etc.</span></span>
* <span data-ttu-id="589f5-129">機能と統合のテスト: 運用環境は、できるだけ次が行われるようにレプリケートすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="589f5-129">Functional and integration testing:  It's important to replicate the production environment as closely as possible to:</span></span>
  * <span data-ttu-id="589f5-130">特定のバージョンまたはエディションのデータベース サーバーを使用している場合にのみ表示される、アプリのイシューを検索する。</span><span class="sxs-lookup"><span data-stu-id="589f5-130">Find issues in the app that only show up when using a specific versions or edition of the database server .</span></span>
  * <span data-ttu-id="589f5-131">EF Core およびその他の依存関係をアップグレードするときに、破壊的変更をキャッチする。</span><span class="sxs-lookup"><span data-stu-id="589f5-131">Catch breaking changes when upgrading EF Core and other dependencies.</span></span> <span data-ttu-id="589f5-132">たとえば、ASP.NET Core、OData、AutoMapper などのフレームワークの追加またはアップグレードです。</span><span class="sxs-lookup"><span data-stu-id="589f5-132">For example, adding or upgrading frameworks like ASP.NET Core, OData, or AutoMapper.</span></span> <span data-ttu-id="589f5-133">これらの依存関係は、予期しない形で EF Core に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="589f5-133">These dependencies can affect EF Core in unexpected ways.</span></span>
* <span data-ttu-id="589f5-134">代表的な負荷によるパフォーマンスとストレス テスト。</span><span class="sxs-lookup"><span data-stu-id="589f5-134">Performance and stress testing with representative loads.</span></span> <span data-ttu-id="589f5-135">一部の機能は、そのまま使用しても、あまりスケーリングされません。</span><span class="sxs-lookup"><span data-stu-id="589f5-135">The naïve usage of some features doesn't scale well.</span></span> <span data-ttu-id="589f5-136">たとえば、複数コレクションのインクルード、遅延読み込みの頻繁な使用、非インデックス列に対する条件付きのクエリ、ストアで生成された値を使用した大規模な更新と挿入、コンカレンシー処理の欠如、大規模なモデル、不適切なキャッシュ ポリシーなどです。</span><span class="sxs-lookup"><span data-stu-id="589f5-136">For example, multiple collections Includes, heavy use of lazy loading, conditional queries on non-indexed columns, massive updates and inserts with store-generated values, lack of concurrency handling, large models, inadequate cache policy.</span></span>
* <span data-ttu-id="589f5-137">セキュリティ レビュー: たとえば、接続文字列やその他のシークレットの処理、非配置操作に対するデータベースのアクセス許可、未加工の SQL の入力検証、機密データの暗号化などです。</span><span class="sxs-lookup"><span data-stu-id="589f5-137">Security review: For example, handling of connection strings and other secrets, database permissions for non-deployment operation, input validation for raw SQL, encryption for sensitive data.</span></span>
* <span data-ttu-id="589f5-138">ログと診断が十分であり、使用可能であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="589f5-138">Make sure logging and diagnostics are sufficient and usable.</span></span> <span data-ttu-id="589f5-139">たとえば、適切なログ構成、クエリ タグ、Application Insights などです。</span><span class="sxs-lookup"><span data-stu-id="589f5-139">For example, appropriate logging configuration, query tags, and Application Insights.</span></span>
* <span data-ttu-id="589f5-140">エラー回復。</span><span class="sxs-lookup"><span data-stu-id="589f5-140">Error recovery.</span></span> <span data-ttu-id="589f5-141">バージョンのロールバック、サーバーのフォールバック、スケールアウトと負荷分散、DoS の軽減、データ バックアップなど、一般的なエラーのシナリオの緊急時に対する準備を行います。</span><span class="sxs-lookup"><span data-stu-id="589f5-141">Prepare contingencies for common failure scenarios such as version rollback, fallback servers, scale-out and load balancing, DoS mitigation, and data backups.</span></span>
* <span data-ttu-id="589f5-142">アプリケーションの配置と移行。</span><span class="sxs-lookup"><span data-stu-id="589f5-142">Application deployment and migration.</span></span> <span data-ttu-id="589f5-143">配置中に移行がどのように適用されるかを計画します。これをアプリケーションの起動時に実行すると、コンカレンシーのイシューが発生し、通常の操作に必要なものより高いアクセス許可が必要になります。</span><span class="sxs-lookup"><span data-stu-id="589f5-143">Plan out how migrations are going to be applied during deployment; doing it at application start can suffer from concurrency issues and requires higher permissions than necessary for normal operation.</span></span> <span data-ttu-id="589f5-144">ステージングを使用して、移行中の致命的なエラーからの回復を容易にします。</span><span class="sxs-lookup"><span data-stu-id="589f5-144">Use staging to facilitate recovery from fatal errors during migration.</span></span> <span data-ttu-id="589f5-145">詳しくは、「[移行の適用](xref:core/managing-schemas/migrations/applying)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="589f5-145">For more information, see [Applying Migrations](xref:core/managing-schemas/migrations/applying).</span></span>
* <span data-ttu-id="589f5-146">生成された移行の詳細な調査とテスト。</span><span class="sxs-lookup"><span data-stu-id="589f5-146">Detailed examination and testing of generated migrations.</span></span> <span data-ttu-id="589f5-147">移行は、運用データに適用する前に十分にテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="589f5-147">Migrations should be thoroughly tested before being applied to production data.</span></span> <span data-ttu-id="589f5-148">テーブルに運用データが格納されると、スキーマの構造と列の型を簡単に変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="589f5-148">The shape of the schema and the column types cannot be easily changed once the tables contain production data.</span></span> <span data-ttu-id="589f5-149">たとえば、SQL Server では、`nvarchar(max)` と `decimal(18, 2)` が文字列と 10 進数のプロパティにマップされた列に最適な型になることはまれですが、EF では特定のシナリオについての知識がないために、既定値として使用されます。</span><span class="sxs-lookup"><span data-stu-id="589f5-149">For example, on SQL Server, `nvarchar(max)` and `decimal(18, 2)` are rarely the best types for columns mapped to string and decimal properties, but those are the defaults that EF uses because it doesn't have knowledge of your specific scenario.</span></span>

## <a name="next-steps"></a><span data-ttu-id="589f5-150">次の手順</span><span class="sxs-lookup"><span data-stu-id="589f5-150">Next steps</span></span>

<span data-ttu-id="589f5-151">入門チュートリアルについては、「[Entity Framework Core の概要](xref:core/get-started/overview/first-app)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="589f5-151">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/overview/first-app).</span></span>
