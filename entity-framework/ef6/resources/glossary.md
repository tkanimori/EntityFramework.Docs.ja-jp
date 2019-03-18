---
title: Entity Framework の用語集 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
ms.openlocfilehash: 4e42e5870879524b814cecdc361e688d36f0180f
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2019
ms.locfileid: "58131384"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="f640e-102">Entity Framework の用語集</span><span class="sxs-lookup"><span data-stu-id="f640e-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="f640e-103">Code First</span><span class="sxs-lookup"><span data-stu-id="f640e-103">Code First</span></span>
<span data-ttu-id="f640e-104">コードを使用して Entity Framework モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f640e-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="f640e-105">既存のデータベースまたは新しいデータベースを対象モデルにできます。</span><span class="sxs-lookup"><span data-stu-id="f640e-105">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="f640e-106">コンテキスト</span><span class="sxs-lookup"><span data-stu-id="f640e-106">Context</span></span>
<span data-ttu-id="f640e-107">クエリを実行し、データを保存することができます、データベースとのセッションを表すクラス。</span><span class="sxs-lookup"><span data-stu-id="f640e-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="f640e-108">コンテキストは、DbContext または ObjectContext クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="f640e-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="f640e-109">規則 (1 つ目のコード)</span><span class="sxs-lookup"><span data-stu-id="f640e-109">Convention (Code First)</span></span>
<span data-ttu-id="f640e-110">Entity Framework を使用して、クラスからのモデルの形状を推測するルール。</span><span class="sxs-lookup"><span data-stu-id="f640e-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="f640e-111">最初のデータベースします。</span><span class="sxs-lookup"><span data-stu-id="f640e-111">Database First</span></span>
<span data-ttu-id="f640e-112">既存のデータベースを対象とする EF デザイナーを使用して、Entity Framework モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f640e-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="f640e-113">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="f640e-113">Eager loading</span></span>
<span data-ttu-id="f640e-114">1 つの型のエンティティのクエリ関連エンティティも読み込むクエリの一部として関連のデータの読み込みのパターン。</span><span class="sxs-lookup"><span data-stu-id="f640e-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="f640e-115">EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="f640e-115">EF Designer</span></span>
<span data-ttu-id="f640e-116">ボックスや線を使用して Entity Framework モデルを作成するための Visual Studio でのビジュアル デザイナーです。</span><span class="sxs-lookup"><span data-stu-id="f640e-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="f640e-117">エンティティ</span><span class="sxs-lookup"><span data-stu-id="f640e-117">Entity</span></span>
<span data-ttu-id="f640e-118">顧客、製品、注文などのアプリケーション データを表すクラスまたはオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="f640e-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="f640e-119">エンティティ データ モデル</span><span class="sxs-lookup"><span data-stu-id="f640e-119">Entity Data Model</span></span>
<span data-ttu-id="f640e-120">エンティティおよびそれらの関係を記述するモデル。</span><span class="sxs-lookup"><span data-stu-id="f640e-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="f640e-121">EF では、EDM を使用して、対象となる概念モデルを記述する開発者プログラム。</span><span class="sxs-lookup"><span data-stu-id="f640e-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="f640e-122">EDM は、Dr で導入されたエンティティ リレーションシップ モデルに基づいています。Peter Chen します。</span><span class="sxs-lookup"><span data-stu-id="f640e-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="f640e-123">EDM は、もともとマイクロソフトの開発者およびサーバー テクノロジのスイート全体で common data model になることの主な目的に開発されました。</span><span class="sxs-lookup"><span data-stu-id="f640e-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="f640e-124">EDM は、OData プロトコルの一部としても使用されます。</span><span class="sxs-lookup"><span data-stu-id="f640e-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="f640e-125">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="f640e-125">Explicit loading</span></span>
<span data-ttu-id="f640e-126">関連オブジェクトが、API を呼び出すことによって読み込まれる関連のデータの読み込みのパターン。</span><span class="sxs-lookup"><span data-stu-id="f640e-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f640e-127">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f640e-127">Fluent API</span></span>
<span data-ttu-id="f640e-128">Code First モデルを構成するために使用できる API。</span><span class="sxs-lookup"><span data-stu-id="f640e-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="f640e-129">外部キーの関連付け</span><span class="sxs-lookup"><span data-stu-id="f640e-129">Foreign key association</span></span>
<span data-ttu-id="f640e-130">依存エンティティのクラスで、外部キーを表すプロパティが含まれている、エンティティ間の関連付け。</span><span class="sxs-lookup"><span data-stu-id="f640e-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="f640e-131">たとえば、製品には、CategoryId プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f640e-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="f640e-132">リレーションシップを識別します。</span><span class="sxs-lookup"><span data-stu-id="f640e-132">Identifying relationship</span></span>
<span data-ttu-id="f640e-133">プリンシパル エンティティの主キーが依存エンティティの主キーの一部であるリレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="f640e-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="f640e-134">このようなリレーションシップでは、プリンシパル エンティティが存在しないと、依存エンティティは存在できません。</span><span class="sxs-lookup"><span data-stu-id="f640e-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="f640e-135">独立アソシエーション</span><span class="sxs-lookup"><span data-stu-id="f640e-135">Independent association</span></span>
<span data-ttu-id="f640e-136">エンティティ間のアソシエーションの依存エンティティ クラスの外部キーを表すプロパティが存在しません。</span><span class="sxs-lookup"><span data-stu-id="f640e-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="f640e-137">たとえば、Product クラスには、カテゴリが CategoryId プロパティへのリレーションシップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f640e-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="f640e-138">Entity Framework では、2 つのアソシエーション end のエンティティの状態とは無関係にアソシエーションの状態を追跡します。</span><span class="sxs-lookup"><span data-stu-id="f640e-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="f640e-139">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="f640e-139">Lazy loading</span></span>
<span data-ttu-id="f640e-140">ナビゲーション プロパティのアクセス時に関連するオブジェクトが読み込ま自動的に関連データの読み込みのパターン。</span><span class="sxs-lookup"><span data-stu-id="f640e-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="f640e-141">まずモデルします。</span><span class="sxs-lookup"><span data-stu-id="f640e-141">Model First</span></span>
<span data-ttu-id="f640e-142">EF デザイナーを使用して、Entity Framework モデルを作成しに使用される新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="f640e-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="f640e-143">ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="f640e-143">Navigation property</span></span>
<span data-ttu-id="f640e-144">別のエンティティを参照するエンティティのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="f640e-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="f640e-145">たとえば、製品にはカテゴリのナビゲーション プロパティが含まれていて、カテゴリには、製品のナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f640e-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="f640e-146">POCO</span><span class="sxs-lookup"><span data-stu-id="f640e-146">POCO</span></span>
<span data-ttu-id="f640e-147">Plain-old CLR オブジェクトの頭字語です。</span><span class="sxs-lookup"><span data-stu-id="f640e-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="f640e-148">任意のフレームワークでの依存関係がないシンプルなユーザー クラスです。</span><span class="sxs-lookup"><span data-stu-id="f640e-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="f640e-149">で、EF EntityObject から派生していない、任意のインターフェイスを実装または EF で定義されているすべての属性を格納するエンティティ クラスのコンテキスト。</span><span class="sxs-lookup"><span data-stu-id="f640e-149">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="f640e-150">「永続化に依存しない」に、永続化フレームワークから分離するこのようなエンティティ クラスともいいます。</span><span class="sxs-lookup"><span data-stu-id="f640e-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="f640e-151">リレーションシップの逆関数</span><span class="sxs-lookup"><span data-stu-id="f640e-151">Relationship inverse</span></span>
<span data-ttu-id="f640e-152">たとえば、製品、リレーションシップの反対側の端。カテゴリとカテゴリ。製品です。</span><span class="sxs-lookup"><span data-stu-id="f640e-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="f640e-153">自己追跡エンティティ</span><span class="sxs-lookup"><span data-stu-id="f640e-153">Self-tracking entity</span></span>
<span data-ttu-id="f640e-154">N 層開発を支援するコード生成テンプレートから構築されるエンティティ。</span><span class="sxs-lookup"><span data-stu-id="f640e-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="f640e-155">テーブル-ごとの具象型 (TPC)</span><span class="sxs-lookup"><span data-stu-id="f640e-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="f640e-156">階層内の各非抽象型が別のデータベース内のテーブルに割り当てられている継承のマッピング方法です。</span><span class="sxs-lookup"><span data-stu-id="f640e-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="f640e-157">-Table-per-hierarchy (TPH)</span><span class="sxs-lookup"><span data-stu-id="f640e-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="f640e-158">階層内のすべての型が、データベースの同じテーブルに割り当てられている継承のマッピング方法です。</span><span class="sxs-lookup"><span data-stu-id="f640e-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="f640e-159">列が行ごとの種類を識別するために使用される識別子が関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="f640e-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="f640e-160">テーブルあたり型 (TPT)</span><span class="sxs-lookup"><span data-stu-id="f640e-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="f640e-161">階層内のすべての型の共通プロパティは、データベースで同じテーブルにマップされますが、各種類に固有のプロパティを別のテーブルにマップする継承のマッピング方法です。</span><span class="sxs-lookup"><span data-stu-id="f640e-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="f640e-162">型の探索</span><span class="sxs-lookup"><span data-stu-id="f640e-162">Type discovery</span></span>
<span data-ttu-id="f640e-163">Entity Framework モデルの一部となる型を識別するプロセス。</span><span class="sxs-lookup"><span data-stu-id="f640e-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>
