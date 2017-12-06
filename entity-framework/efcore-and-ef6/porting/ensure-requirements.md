---
title: "EF6 から EF Core - への移植要件の検証"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 2f45039e63546d266ec6ce0bfa66ef7e9fb3d7e7
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="6d7f2-102">EF6 から EF コアへの移植する前に: アプリケーションの要件の検証</span><span class="sxs-lookup"><span data-stu-id="6d7f2-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="6d7f2-103">移植のプロセスを開始する前に、EF コアが、アプリケーションのデータ アクセス要件を満たしていることを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="6d7f2-104">足りない機能</span><span class="sxs-lookup"><span data-stu-id="6d7f2-104">Missing features</span></span>

<span data-ttu-id="6d7f2-105">EF 中核となるすべての機能をアプリケーションで使用する必要があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="6d7f2-106">参照してください[機能の比較](../features.md)EF6 に EF のコア機能を比較する方法の詳細な比較についてはします。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="6d7f2-107">任意の必要な機能が不足している場合は、EF コアへの移植する前にこれらの機能の欠落を補うことができますを確認します。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="6d7f2-108">動作の変更</span><span class="sxs-lookup"><span data-stu-id="6d7f2-108">Behavior changes</span></span>

<span data-ttu-id="6d7f2-109">これは、網羅 EF6 と EF コア間の動作のいくつかの変更の一覧です。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="6d7f2-110">保持する以下の点に、ポートとして、アプリケーション、アプリケーションの動作は表示されませんコンパイル エラーとしてスワップすると、EF コアに方法が変わる可能性がある重要です。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="6d7f2-111">DbSet.Add/Attach とグラフの動作</span><span class="sxs-lookup"><span data-stu-id="6d7f2-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="6d7f2-112">EF6 でを呼び出す`DbSet.Add()`ナビゲーション プロパティで参照されているすべてのエンティティの再帰的な検索で、エンティティの結果にします。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="6d7f2-113">任意のエンティティが検出され、コンテキストによって既に追跡されていないにも追加としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-113">Any entities that are found, and are not already tracked by the context, are also be marked as added.</span></span> <span data-ttu-id="6d7f2-114">`DbSet.Attach()`すべてのエンティティがマークされている以外の動作は同じで、変更します。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="6d7f2-115">**EF コアのような再帰検索を実行するが、若干異なるルールがいくつか。**</span><span class="sxs-lookup"><span data-stu-id="6d7f2-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="6d7f2-116">ルート エンティティが要求された状態では常に (の追加`DbSet.Add`変更しなかったと`DbSet.Attach`)。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="6d7f2-117">**エンティティのナビゲーション プロパティの再帰検索中に存在します。**</span><span class="sxs-lookup"><span data-stu-id="6d7f2-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="6d7f2-118">**エンティティの主キーがストアで生成された場合**</span><span class="sxs-lookup"><span data-stu-id="6d7f2-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="6d7f2-119">主キーが値に設定されていない場合、状態が追加に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="6d7f2-120">主キーの値と見なされます「未設定」プロパティの型の CLR の既定値が割り当てられます (つまり`0`の`int`、`null`の`string`, などです。)。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (i.e. `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="6d7f2-121">主キーが値に設定されている場合、状態に設定変更されません。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="6d7f2-122">主キーは、生成されたデータベースではありません、エンティティは、ルートと同じ状態で配置されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="6d7f2-123">コードの最初のデータベースの初期化</span><span class="sxs-lookup"><span data-stu-id="6d7f2-123">Code First database initialization</span></span>

<span data-ttu-id="6d7f2-124">**EF6 膨大な量のデータベース接続を選択すると、データベースの初期化を実行するマジックがあります。これらのルールのとおりです。**</span><span class="sxs-lookup"><span data-stu-id="6d7f2-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="6d7f2-125">構成が行われない場合、EF6 は SQL Express または LocalDb にデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="6d7f2-126">コンテキストと同じ名前で接続文字列が、アプリケーションの場合`App/Web.config`ファイルでは、この接続が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="6d7f2-127">データベースが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="6d7f2-128">存在しない場合、テーブル モデルからのデータベースでは、現在のモデルのスキーマがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="6d7f2-129">移行が有効な場合は、データベースの作成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="6d7f2-130">データベースが存在するし、EF6 以前に作成した、スキーマ、スキーマが現在のモデルとの互換性チェックされます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="6d7f2-131">スキーマが作成されたため、モデルが変更された場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="6d7f2-132">**この秘密のいずれかの EF コアは実行されません。**</span><span class="sxs-lookup"><span data-stu-id="6d7f2-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="6d7f2-133">コードでは、データベース接続を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="6d7f2-134">初期化は実行されません。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-134">No initialization is performed.</span></span> <span data-ttu-id="6d7f2-135">使用する必要があります`DbContext.Database.Migrate()`移行を適用する (または`DbContext.Database.EnsureCreated()`と`EnsureDeleted()`への移行を使用せず、データベースの作成/削除) します。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="6d7f2-136">コードの最初のテーブルの名前付け規則</span><span class="sxs-lookup"><span data-stu-id="6d7f2-136">Code First table naming convention</span></span>

<span data-ttu-id="6d7f2-137">EF6 には、エンティティにマップされる既定のテーブル名を計算する複数形化サービスからエンティティ クラス名が実行されます。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="6d7f2-138">名前を使用する EF コア、`DbSet`派生のコンテキストにエンティティが公開されているプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="6d7f2-139">エンティティがない場合、`DbSet`プロパティ後でクラス名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6d7f2-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
