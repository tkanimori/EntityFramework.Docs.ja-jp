---
title: キーなしエンティティ型-EF Core
description: Entity Framework Core を使用してキーなしエンティティ型を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 129e24b154ba32583435aeb742dbf478350344e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824665"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="13317-103">キーなしエンティティ型</span><span class="sxs-lookup"><span data-stu-id="13317-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="13317-104">この機能は、EF Core 2.1 で、クエリの種類の名前の下に追加されました。</span><span class="sxs-lookup"><span data-stu-id="13317-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="13317-105">EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="13317-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="13317-106">EF Core モデルには、通常のエンティティ型に加えて、_キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="13317-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="13317-107">キーなしエンティティ型の特性</span><span class="sxs-lookup"><span data-stu-id="13317-107">Keyless entity types characteristics</span></span>

<span data-ttu-id="13317-108">キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="13317-108">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="13317-109">リレーショナル ストアで、ターゲット データベースのオブジェクトと列 fluent API のメソッドまたはデータの注釈を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13317-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="13317-110">ただし、これらは通常のエンティティ型とは異なります。</span><span class="sxs-lookup"><span data-stu-id="13317-110">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="13317-111">キーを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="13317-111">Cannot have a key defined.</span></span>
- <span data-ttu-id="13317-112">は_Dbcontext_の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="13317-112">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="13317-113">規則によって検出されたことはありません。</span><span class="sxs-lookup"><span data-stu-id="13317-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="13317-114">では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、</span><span class="sxs-lookup"><span data-stu-id="13317-114">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="13317-115">リレーションシップのプリンシパル end として機能しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13317-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="13317-116">所有しているエンティティに対するナビゲーションが含まれていない可能性があります</span><span class="sxs-lookup"><span data-stu-id="13317-116">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="13317-117">これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13317-117">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="13317-118">エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="13317-118">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="13317-119">`.HasNoKey()` メソッド呼び出しを使用して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13317-119">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="13317-120">を_定義するクエリ_にマップできます。</span><span class="sxs-lookup"><span data-stu-id="13317-120">May be mapped to a _defining query_.</span></span> <span data-ttu-id="13317-121">定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。</span><span class="sxs-lookup"><span data-stu-id="13317-121">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="13317-122">使用シナリオ</span><span class="sxs-lookup"><span data-stu-id="13317-122">Usage scenarios</span></span>

<span data-ttu-id="13317-123">キーなしエンティティ型の主な使用シナリオの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="13317-123">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="13317-124">[生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="13317-124">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="13317-125">主キーが含まれていないデータベースビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="13317-125">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="13317-126">定義された主キーがないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="13317-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="13317-127">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="13317-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="13317-128">データベース オブジェクトへのマッピング</span><span class="sxs-lookup"><span data-stu-id="13317-128">Mapping to database objects</span></span>

<span data-ttu-id="13317-129">キーなしエンティティ型をデータベースオブジェクトにマップするには、`ToTable` または `ToView` fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="13317-129">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="13317-130">このメソッドで指定されたデータベース オブジェクトは、EF Core の観点から、_ビュー_、読み取り専用クエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作。</span><span class="sxs-lookup"><span data-stu-id="13317-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="13317-131">ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="13317-131">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="13317-132">または、読み取り専用として扱われるデータベーステーブルにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="13317-132">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="13317-133">逆に、通常のエンティティ型の場合、EF Core は、`ToTable` メソッドに指定されたデータベースオブジェクトを_テーブル_として扱うことができることを前提としています。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13317-133">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="13317-134">実際には、データベース ビューの名前を指定できます`ToTable`データベースに対して更新可能にするビューが構成されている限り問題なく機能すべて。</span><span class="sxs-lookup"><span data-stu-id="13317-134">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="13317-135">`ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="13317-135">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="13317-136">使用例</span><span class="sxs-lookup"><span data-stu-id="13317-136">Example</span></span>

<span data-ttu-id="13317-137">次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="13317-137">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="13317-138">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="13317-138">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="13317-139">最初に、ブログや投稿の単純なモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="13317-139">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="13317-140">次に、各ブログに関連する投稿の数を照会することを許可する単純なデータベース ビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="13317-140">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="13317-141">次に、データベース ビューから結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="13317-141">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="13317-142">次に、`HasNoKey` API を使用して、 _Onmodelcreating_エンティティ型を構成します。</span><span class="sxs-lookup"><span data-stu-id="13317-142">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="13317-143">Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="13317-143">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="13317-144">次に、`DbSet<T>`を含めるように `DbContext` を構成します。</span><span class="sxs-lookup"><span data-stu-id="13317-144">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="13317-145">最後に、標準的な方法で、データベース ビューを照会できます。</span><span class="sxs-lookup"><span data-stu-id="13317-145">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="13317-146">また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。</span><span class="sxs-lookup"><span data-stu-id="13317-146">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
