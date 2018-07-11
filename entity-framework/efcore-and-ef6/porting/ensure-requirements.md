---
title: EF6 から EF Core - への移植要件の検証
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 65bdc8bb9574d37db697aa47c8e8c480cefcb4f7
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949115"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="f1041-102">EF6 から EF Core へ移植する前に: アプリケーションの要件の検証</span><span class="sxs-lookup"><span data-stu-id="f1041-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="f1041-103">移植プロセスを開始する前に、EF Core が、アプリケーションのデータ アクセス要件を満たしていることを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1041-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="f1041-104">不足している機能</span><span class="sxs-lookup"><span data-stu-id="f1041-104">Missing features</span></span>

<span data-ttu-id="f1041-105">EF Core が、アプリケーションで使用する必要がありますすべての機能を持つことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f1041-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="f1041-106">参照してください[機能の比較](../features.md)EF Core での機能を EF6 を比較する方法の詳細な比較。</span><span class="sxs-lookup"><span data-stu-id="f1041-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="f1041-107">任意の必要な機能が存在しない場合は、EF Core に移植する前にこれらの機能の欠落を補うことができますを確認します。</span><span class="sxs-lookup"><span data-stu-id="f1041-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="f1041-108">動作の変更</span><span class="sxs-lookup"><span data-stu-id="f1041-108">Behavior changes</span></span>

<span data-ttu-id="f1041-109">これは、EF6 と EF Core 間における動作のいくつかの変更の一部の一覧です。</span><span class="sxs-lookup"><span data-stu-id="f1041-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="f1041-110">留意してポートとして、アプリケーション、アプリケーションの動作は表示されませんコンパイル エラーとしてスワップすると、EF Core への方法が変わる可能性がある重要です。</span><span class="sxs-lookup"><span data-stu-id="f1041-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="f1041-111">DbSet.Add/Attach とグラフの動作</span><span class="sxs-lookup"><span data-stu-id="f1041-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="f1041-112">EF6 では、呼び出す`DbSet.Add()`ナビゲーション プロパティで参照されているすべてのエンティティの再帰的な検索でエンティティの結果にします。</span><span class="sxs-lookup"><span data-stu-id="f1041-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="f1041-113">検出され、コンテキストによって既に追跡されていない任意のエンティティも追加としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="f1041-113">Any entities that are found, and are not already tracked by the context, are also be marked as added.</span></span> <span data-ttu-id="f1041-114">`DbSet.Attach()` すべてのエンティティがマークされている点を除いて同じですが、動作に変更されません。</span><span class="sxs-lookup"><span data-stu-id="f1041-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="f1041-115">**EF Core と同様の再帰検索を実行しますが、いくつかの若干異なる規則。**</span><span class="sxs-lookup"><span data-stu-id="f1041-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="f1041-116">ルート エンティティが要求された状態では常に (追加`DbSet.Add`の変更と`DbSet.Attach`)。</span><span class="sxs-lookup"><span data-stu-id="f1041-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="f1041-117">**エンティティのナビゲーション プロパティの再帰検索中に存在します。**</span><span class="sxs-lookup"><span data-stu-id="f1041-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="f1041-118">**エンティティの主キーが生成されるストアの場合**</span><span class="sxs-lookup"><span data-stu-id="f1041-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="f1041-119">主キーが値に設定されていない場合は、状態に追加された設定されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="f1041-120">主キーの値と見なされます「設定なし」プロパティの型の CLR の既定値が割り当てられている場合 (たとえば、`0`の`int`、`null`の`string`など。)。</span><span class="sxs-lookup"><span data-stu-id="f1041-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="f1041-121">主キーが値に設定されている場合、状態が unchanged に設定します。</span><span class="sxs-lookup"><span data-stu-id="f1041-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="f1041-122">主キーがデータベースで生成されたでない場合は、エンティティは、ルートと同じ状態に置かれます。</span><span class="sxs-lookup"><span data-stu-id="f1041-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="f1041-123">コードの最初のデータベースの初期化</span><span class="sxs-lookup"><span data-stu-id="f1041-123">Code First database initialization</span></span>

<span data-ttu-id="f1041-124">**EF6 が、大量のマジックのデータベース接続の選択やデータベースの初期化を実行します。これらのルールのとおりです。**</span><span class="sxs-lookup"><span data-stu-id="f1041-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="f1041-125">構成がない場合、EF6 は SQL Express または LocalDb にデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="f1041-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="f1041-126">コンテキストと同じ名前で接続文字列が、アプリケーションの場合`App/Web.config`ファイルでは、この接続が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="f1041-127">データベースが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="f1041-128">テーブル モデルからは、データベースに存在する場合は、現在のモデルのスキーマがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="f1041-129">Migrations を有効にする場合は、データベースの作成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="f1041-130">場合は、データベースが存在し、EF6 以前に作成した、スキーマ、スキーマを現在のモデルとの互換性をチェックします。</span><span class="sxs-lookup"><span data-stu-id="f1041-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="f1041-131">スキーマが作成されたため、モデルが変更された場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f1041-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="f1041-132">**このマジックのいずれかの EF Core は実行されません。**</span><span class="sxs-lookup"><span data-stu-id="f1041-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="f1041-133">コードでは、データベース接続を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1041-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="f1041-134">初期化は実行されません。</span><span class="sxs-lookup"><span data-stu-id="f1041-134">No initialization is performed.</span></span> <span data-ttu-id="f1041-135">使用する必要があります`DbContext.Database.Migrate()`移行を適用する (または`DbContext.Database.EnsureCreated()`と`EnsureDeleted()`migrations を使用せず、データベースの作成/削除する)。</span><span class="sxs-lookup"><span data-stu-id="f1041-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="f1041-136">名前付け規則、コードの最初のテーブル</span><span class="sxs-lookup"><span data-stu-id="f1041-136">Code First table naming convention</span></span>

<span data-ttu-id="f1041-137">EF6 では、エンティティにマップされる既定のテーブル名を計算する複数形化サービスを介してエンティティ クラス名が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f1041-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="f1041-138">EF Core の名前を使用して、`DbSet`エンティティが、派生コンテキストで公開されているプロパティ。</span><span class="sxs-lookup"><span data-stu-id="f1041-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="f1041-139">エンティティがない場合、`DbSet`し、プロパティ、クラス名を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1041-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
