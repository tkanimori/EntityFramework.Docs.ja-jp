---
title: エンティティの状態 - EF6 の使用
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: c1dde7810d1dfa8a73e6bd2cf091b24be6b865d8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490666"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="fa8b5-102">エンティティの状態の操作</span><span class="sxs-lookup"><span data-stu-id="fa8b5-102">Working with entity states</span></span>
<span data-ttu-id="fa8b5-103">このトピックでは、追加し、コンテキストにエンティティを接続する方法、および Entity Framework が SaveChanges 中にこれらを処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="fa8b5-104">Entity Framework で、コンテキストに接続されている、または未接続の N 層シナリオでは EF にどのような状態のエンティティを通知させることができますが、エンティティの状態であるか追跡処理されます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="fa8b5-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="fa8b5-106">エンティティの状態および SaveChanges</span><span class="sxs-lookup"><span data-stu-id="fa8b5-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="fa8b5-107">EntityState 列挙体によって定義されている 5 つの状態のいずれかでエンティティができます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="fa8b5-108">これらの状態は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-108">These states are:</span></span>  

- <span data-ttu-id="fa8b5-109">: 追加されたエンティティがコンテキストによって追跡されているが、データベースに存在しません</span><span class="sxs-lookup"><span data-stu-id="fa8b5-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="fa8b5-110">Unchanged: エンティティがコンテキストによって追跡されていると、データベースに存在して、プロパティの値が、データベース内の値から変更されていません</span><span class="sxs-lookup"><span data-stu-id="fa8b5-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="fa8b5-111">更新日時: エンティティがコンテキストによって追跡されていると、データベースに存在して、プロパティの値の一部またはすべてが変更されました。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="fa8b5-112">削除: エンティティがコンテキストによって追跡されていると、データベースに存在しますが、次回に SaveChanges が呼び出されたとき、データベースから削除とマークされています。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="fa8b5-113">: デタッチされたエンティティがないコンテキストによって追跡されて、</span><span class="sxs-lookup"><span data-stu-id="fa8b5-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="fa8b5-114">SaveChanges はさまざまな状態のエンティティのさまざまな処理を行います。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="fa8b5-115">SaveChanges では、変更されていないエンティティは変更されません。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="fa8b5-116">更新プログラムは、エンティティ、Unchanged 状態のデータベースには送信されません。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="fa8b5-117">追加エンティティは、データベースに挿入され、しになる変更されずに SaveChanges を返します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="fa8b5-118">変更されたエンティティは、データベースが更新され、しになる変更されずに SaveChanges を返します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="fa8b5-119">削除されたエンティティは、データベースから削除して、コンテキストからデタッチされますし。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="fa8b5-120">次の例では、エンティティまたはエンティティ グラフの状態を変更できる方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="fa8b5-121">新しいエンティティをコンテキストに追加します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="fa8b5-122">新しいエンティティは、DbSet の Add メソッドを呼び出すことによって、コンテキストに追加できます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="fa8b5-123">これは、エンティティを Added 状態で、つまりことにはデータベースに挿入される、次回の SaveChanges が呼び出されるとします。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="fa8b5-124">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-125">コンテキストに新しいエンティティを追加する別の方法では、Added にその状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="fa8b5-126">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-127">最後に、別のエンティティは既に追跡されている最大フックして、コンテキストに新しいエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="fa8b5-128">別のエンティティのコレクション ナビゲーション プロパティに新しいエンティティを追加するか、新しいエンティティをポイントする別のエンティティの参照ナビゲーション プロパティを設定することが考えられます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="fa8b5-129">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    var blog = context.Blogs.Find(2);
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-130">追跡し、これらの新しいエンティティがまだ追加されているエンティティがないその他のエンティティへの参照を持つ場合、これらの例のすべてのことは、コンテキストにも追加され、次回 SaveChanges が呼び出されるとに、データベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="fa8b5-131">既存のエンティティをコンテキストにアタッチ</span><span class="sxs-lookup"><span data-stu-id="fa8b5-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="fa8b5-132">ある場合は、データベースが、これは現在によって追跡されていないコンテキスト DbSet の Attach メソッドを使用してエンティティを追跡するためにコンテキストを指示に既にわかっているエンティティが存在します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="fa8b5-133">エンティティは、コンテキストで変更されていない状態になります。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="fa8b5-134">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-135">変更されないことをデータベースに接続されているエンティティの他の操作を実行せずに SaveChanges が呼び出された場合に注意してください。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="fa8b5-136">これは、エンティティが Unchanged 状態であるためにです。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="fa8b5-137">既存のエンティティをコンテキストにアタッチする別の方法では、Unchanged にその状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="fa8b5-138">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-139">これらの例の両方にアタッチされるエンティティがまだ追跡されていない他のエンティティへの参照を持つ場合、これらの新しいエンティティもに接続されている Unchanged 状態でコンテキストに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="fa8b5-140">コンテキストに変更されたエンティティが、既存のアタッチ</span><span class="sxs-lookup"><span data-stu-id="fa8b5-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="fa8b5-141">ある場合は、どの変更可能性がありますが行われたエンティティをアタッチし、その状態を Modified に設定するコンテキストを指示するが、データベース内に既にわかっているエンティティが存在します。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="fa8b5-142">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-143">Modified 状態を変更して、エンティティのすべてのプロパティは変更済みとしてマークはすべてのプロパティ値は、SaveChanges が呼び出されたときに、データベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="fa8b5-144">アタッチされるエンティティにまだ追跡されていない他のエンティティへの参照がある場合は、し、これらの新しいエンティティが接続されている Unchanged 状態でコンテキストに注意してください: ない自動的に行うことが変更されました。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="fa8b5-145">変更済みとしてマークする必要がある複数のエンティティがある場合の各エンティティの状態を個別に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="fa8b5-146">追跡対象エンティティの状態の変更</span><span class="sxs-lookup"><span data-stu-id="fa8b5-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="fa8b5-147">エントリに、State プロパティを設定して追跡されるエンティティの状態を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="fa8b5-148">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-148">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="fa8b5-149">既に追跡されているエンティティの追加またはアタッチを呼び出すこともでき、エンティティの状態を変更するに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="fa8b5-150">たとえば、Added 状態であるエンティティの Attach の呼び出しはその状態を変更 Unchanged。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="fa8b5-151">挿入または更新パターン</span><span class="sxs-lookup"><span data-stu-id="fa8b5-151">Insert or update pattern</span></span>  

<span data-ttu-id="fa8b5-152">一部のアプリケーションの一般的なパターンは、新しい (その結果、データベースの挿入) としてエンティティを追加するか、またはとして既存のエンティティをアタッチし、(その結果、データベースの更新) を変更済みとしてマークする主キーの値によって決まります。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="fa8b5-153">たとえば、データベースで生成された整数の主キーを使用する場合は、新規として 0 個のキーを持つエンティティと既存として 0 以外のキーを持つエンティティを処理する一般的なは。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="fa8b5-154">このパターンは、主キーの値のチェックに基づくエンティティの状態を設定して実現できます。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="fa8b5-155">例えば:</span><span class="sxs-lookup"><span data-stu-id="fa8b5-155">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="fa8b5-156">Modified 状態を変更すると、エンティティのすべてのプロパティは変更済みとしてマークされ、すべてのプロパティ値は、SaveChanges が呼び出されたときに、データベースに送信されますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fa8b5-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
