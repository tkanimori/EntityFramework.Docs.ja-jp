---
title: EF6 から EF Core への移植-要件の検証
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 07caa39e8a56db71e493331ea9f0286309abc52a
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565350"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="08757-102">EF6 から EF Core に移植する前に:アプリケーションの要件を検証する</span><span class="sxs-lookup"><span data-stu-id="08757-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="08757-103">移植プロセスを開始する前に、EF Core がアプリケーションのデータアクセス要件を満たしていることを検証することが重要です。</span><span class="sxs-lookup"><span data-stu-id="08757-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="08757-104">不足している機能</span><span class="sxs-lookup"><span data-stu-id="08757-104">Missing features</span></span>

<span data-ttu-id="08757-105">EF Core に、アプリケーションで使用する必要があるすべての機能が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="08757-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="08757-106">EF Core の機能セットと EF6 の比較の詳細については、「[機能の比較](../features.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08757-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="08757-107">必要な機能が不足している場合は、EF Core に移植する前に、これらの機能が不足していないかどうかを補償できることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="08757-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="08757-108">動作の変更</span><span class="sxs-lookup"><span data-stu-id="08757-108">Behavior changes</span></span>

<span data-ttu-id="08757-109">これは、EF6 と EF Core の動作のいくつかの変更の完全な一覧ではありません。</span><span class="sxs-lookup"><span data-stu-id="08757-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="08757-110">アプリケーションの動作方法は変更される可能性がありますが、EF Core にスワップした後はコンパイルエラーとして表示されないため、これらの点に注意することが重要です。</span><span class="sxs-lookup"><span data-stu-id="08757-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="08757-111">DbSet。追加/アタッチとグラフ動作</span><span class="sxs-lookup"><span data-stu-id="08757-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="08757-112">EF6 では、 `DbSet.Add()`エンティティに対してを呼び出すと、そのナビゲーションプロパティで参照されるすべてのエンティティに対して再帰的な検索が実行されます。</span><span class="sxs-lookup"><span data-stu-id="08757-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="08757-113">既にコンテキストによって追跡されていないエンティティも、追加済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="08757-113">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="08757-114">`DbSet.Attach()`すべてのエンティティが未変更としてマークされている場合を除き、同じ動作が行われます。</span><span class="sxs-lookup"><span data-stu-id="08757-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="08757-115">**EF Core は同様の再帰検索を実行しますが、若干異なるルールを使用します。**</span><span class="sxs-lookup"><span data-stu-id="08757-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="08757-116">ルートエンティティは常に要求された状態になり`DbSet.Add`ます (用`DbSet.Attach`に追加され、に変更はありません)。</span><span class="sxs-lookup"><span data-stu-id="08757-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="08757-117">**ナビゲーションプロパティの再帰的な検索時に検出されるエンティティの場合:**</span><span class="sxs-lookup"><span data-stu-id="08757-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="08757-118">**エンティティの主キーがストアで生成される場合**</span><span class="sxs-lookup"><span data-stu-id="08757-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="08757-119">主キーが値に設定されていない場合、状態は "追加済み" に設定されます。</span><span class="sxs-lookup"><span data-stu-id="08757-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="08757-120">主キーの値は、プロパティの型に CLR `0`の既定値 ( `int` `null` `string`たとえば、for、など) が割り当てられている場合、"設定されていません" と見なされます。</span><span class="sxs-lookup"><span data-stu-id="08757-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="08757-121">主キーが値に設定されている場合、状態は unchanged に設定されます。</span><span class="sxs-lookup"><span data-stu-id="08757-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="08757-122">主キーがデータベースで生成されていない場合、エンティティはルートと同じ状態になります。</span><span class="sxs-lookup"><span data-stu-id="08757-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="08757-123">Code First データベースの初期化</span><span class="sxs-lookup"><span data-stu-id="08757-123">Code First database initialization</span></span>

<span data-ttu-id="08757-124">**EF6 は、データベース接続を選択し、データベースを初期化するために、大量のマジックを実行します。これらの規則には、次のようなものがあります。**</span><span class="sxs-lookup"><span data-stu-id="08757-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="08757-125">構成を実行しない場合、EF6 は SQL Express または LocalDb のデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="08757-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="08757-126">コンテキストと同じ名前の接続文字列がアプリケーション`App/Web.config`ファイル内にある場合は、この接続が使用されます。</span><span class="sxs-lookup"><span data-stu-id="08757-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="08757-127">データベースが存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="08757-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="08757-128">モデルのテーブルがデータベースに存在しない場合は、現在のモデルのスキーマがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="08757-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="08757-129">移行が有効になっている場合は、データベースの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="08757-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="08757-130">データベースが存在し、EF6 によってスキーマが既に作成されている場合は、スキーマに現在のモデルとの互換性があるかどうかがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="08757-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="08757-131">スキーマの作成後にモデルが変更された場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="08757-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="08757-132">**EF Core では、このマジックは実行されません。**</span><span class="sxs-lookup"><span data-stu-id="08757-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="08757-133">データベース接続は、コードで明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08757-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="08757-134">初期化は実行されません。</span><span class="sxs-lookup"><span data-stu-id="08757-134">No initialization is performed.</span></span> <span data-ttu-id="08757-135">移行を適用`DbContext.Database.Migrate()`するにはを使用`DbContext.Database.EnsureCreated()`し、移行を使用せずにデータベースを作成または削除するには、 `EnsureDeleted()`を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08757-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="08757-136">Code First テーブルの名前付け規則</span><span class="sxs-lookup"><span data-stu-id="08757-136">Code First table naming convention</span></span>

<span data-ttu-id="08757-137">EF6 は、複数形化サービスを介してエンティティクラス名を実行し、エンティティがマップされる既定のテーブル名を計算します。</span><span class="sxs-lookup"><span data-stu-id="08757-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="08757-138">EF Core は、派生コンテキストで`DbSet`エンティティが公開されるプロパティの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="08757-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="08757-139">エンティティに`DbSet`プロパティがない場合は、クラス名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="08757-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
