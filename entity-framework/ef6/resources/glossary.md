---
title: Entity Framework 用語集-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: df0da4a68b3d2c882d9673417ee5fe335eccae2b
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656154"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="b2d9c-102">Entity Framework 用語集</span><span class="sxs-lookup"><span data-stu-id="b2d9c-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="b2d9c-103">Code First</span><span class="sxs-lookup"><span data-stu-id="b2d9c-103">Code First</span></span>
<span data-ttu-id="b2d9c-104">コードを使用して Entity Framework モデルを作成する。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="b2d9c-105">モデルでは、既存のデータベースまたは新しいデータベースを対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-105">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="b2d9c-106">コンテキスト</span><span class="sxs-lookup"><span data-stu-id="b2d9c-106">Context</span></span>
<span data-ttu-id="b2d9c-107">データベースとのセッションを表すクラス。これにより、データのクエリと保存を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="b2d9c-108">コンテキストは、DbContext または ObjectContext クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="b2d9c-109">規則 (Code First)</span><span class="sxs-lookup"><span data-stu-id="b2d9c-109">Convention (Code First)</span></span>
<span data-ttu-id="b2d9c-110">クラスからモデルの形状を推論するために Entity Framework 使用する規則。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="b2d9c-111">Database First</span><span class="sxs-lookup"><span data-stu-id="b2d9c-111">Database First</span></span>
<span data-ttu-id="b2d9c-112">EF デザイナーを使用して、既存のデータベースを対象とする Entity Framework モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="b2d9c-113">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="b2d9c-113">Eager loading</span></span>
<span data-ttu-id="b2d9c-114">ある種類のエンティティに対するクエリもクエリの一部として関連エンティティを読み込む、関連データを読み込むパターン。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="b2d9c-115">EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="b2d9c-115">EF Designer</span></span>
<span data-ttu-id="b2d9c-116">ボックスと行を使用して Entity Framework モデルを作成できる Visual Studio のビジュアルデザイナー。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="b2d9c-117">エンティティ</span><span class="sxs-lookup"><span data-stu-id="b2d9c-117">Entity</span></span>
<span data-ttu-id="b2d9c-118">顧客、製品、注文などのアプリケーション データを表すクラスまたはオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="b2d9c-119">エンティティ データ モデル</span><span class="sxs-lookup"><span data-stu-id="b2d9c-119">Entity Data Model</span></span>
<span data-ttu-id="b2d9c-120">エンティティとその間のリレーションシップを記述するモデル。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="b2d9c-121">EF では、EDM を使用して、開発者がプログラムを実行する概念モデルを記述します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="b2d9c-122">EDM は、Dr. Peter Chen によって導入されたエンティティリレーションシップモデルに基づいて構築されます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="b2d9c-123">EDM は、Microsoft の開発者およびサーバーテクノロジのスイート全体で共通のデータモデルになるという主な目標を使用して開発されました。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="b2d9c-124">EDM は、OData プロトコルの一部としても使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b2d9c-125">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="b2d9c-125">Explicit loading</span></span>
<span data-ttu-id="b2d9c-126">API を呼び出すことによって関連オブジェクトが読み込まれる場合に関連するデータを読み込むパターン。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="b2d9c-127">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b2d9c-127">Fluent API</span></span>
<span data-ttu-id="b2d9c-128">Code First モデルを構成するために使用できる API。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="b2d9c-129">外部キーの関連付け</span><span class="sxs-lookup"><span data-stu-id="b2d9c-129">Foreign key association</span></span>
<span data-ttu-id="b2d9c-130">外部キーを表すプロパティが依存エンティティのクラスに含まれるエンティティ間のアソシエーション。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="b2d9c-131">たとえば、Product には CategoryId プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="b2d9c-132">リレーションシップの識別</span><span class="sxs-lookup"><span data-stu-id="b2d9c-132">Identifying relationship</span></span>
<span data-ttu-id="b2d9c-133">プリンシパル エンティティの主キーが依存エンティティの主キーの一部であるリレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="b2d9c-134">このようなリレーションシップでは、プリンシパル エンティティが存在しないと、依存エンティティは存在できません。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="b2d9c-135">独立関連付け</span><span class="sxs-lookup"><span data-stu-id="b2d9c-135">Independent association</span></span>
<span data-ttu-id="b2d9c-136">依存エンティティのクラスの外部キーを表すプロパティがないエンティティ間のアソシエーション。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="b2d9c-137">たとえば、Product クラスには Category へのリレーションシップが含まれていますが、CategoryId プロパティはありません。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="b2d9c-138">Entity Framework は、2つのアソシエーション end のエンティティの状態に関係なく、関連付けの状態を追跡します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="b2d9c-139">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="b2d9c-139">Lazy loading</span></span>
<span data-ttu-id="b2d9c-140">ナビゲーションプロパティにアクセスしたときに関連オブジェクトが自動的に読み込まれる関連データの読み込みパターン。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="b2d9c-141">Model First</span><span class="sxs-lookup"><span data-stu-id="b2d9c-141">Model First</span></span>
<span data-ttu-id="b2d9c-142">EF デザイナーを使用して Entity Framework モデルを作成し、その後、新しいデータベースを作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="b2d9c-143">ナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="b2d9c-143">Navigation property</span></span>
<span data-ttu-id="b2d9c-144">別のエンティティを参照するエンティティのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="b2d9c-145">たとえば、Product に Category ナビゲーションプロパティが含まれていて、Category に Products ナビゲーションプロパティが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="b2d9c-146">POCO</span><span class="sxs-lookup"><span data-stu-id="b2d9c-146">POCO</span></span>
<span data-ttu-id="b2d9c-147">Plain Old CLR Object の頭字語。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="b2d9c-148">任意のフレームワークとの依存関係を持たない単純なユーザークラス。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="b2d9c-149">EF のコンテキストでは、EntityObject から派生していないエンティティクラスは、任意のインターフェイスを実装するか、EF で定義されているすべての属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-149">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="b2d9c-150">永続化フレームワークから切り離されたこのようなエンティティクラスは、"永続化に依存しない" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="b2d9c-151">リレーションシップの逆</span><span class="sxs-lookup"><span data-stu-id="b2d9c-151">Relationship inverse</span></span>
<span data-ttu-id="b2d9c-152">リレーションシップの反対側 (product など)。カテゴリとカテゴリ。梱包.</span><span class="sxs-lookup"><span data-stu-id="b2d9c-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="b2d9c-153">自己追跡エンティティ</span><span class="sxs-lookup"><span data-stu-id="b2d9c-153">Self-tracking entity</span></span>
<span data-ttu-id="b2d9c-154">N 層の開発に役立つコード生成テンプレートから構築されたエンティティ。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="b2d9c-155">具象型ごとのテーブル (TPC)</span><span class="sxs-lookup"><span data-stu-id="b2d9c-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="b2d9c-156">階層内の各非抽象型がデータベース内の別のテーブルにマップされる継承をマッピングする方法。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="b2d9c-157">階層ごとのテーブル (TPH)</span><span class="sxs-lookup"><span data-stu-id="b2d9c-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="b2d9c-158">階層内のすべての型がデータベース内の同じテーブルにマップされる継承をマッピングする方法。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="b2d9c-159">識別子列は、各行が関連付けられている型を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="b2d9c-160">型ごとのテーブル (TPT)</span><span class="sxs-lookup"><span data-stu-id="b2d9c-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="b2d9c-161">階層内のすべての型の共通プロパティがデータベース内の同じテーブルにマップされる継承をマッピングする方法。ただし、それぞれの型に固有のプロパティは、別のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="b2d9c-162">種類の検出</span><span class="sxs-lookup"><span data-stu-id="b2d9c-162">Type discovery</span></span>
<span data-ttu-id="b2d9c-163">Entity Framework モデルに含める必要がある型を識別するプロセス。</span><span class="sxs-lookup"><span data-stu-id="b2d9c-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>
