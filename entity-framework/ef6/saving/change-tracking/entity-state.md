---
title: エンティティの状態の操作-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: ef0e8d5a5a9d66adab7046088c49d8cd472edc8a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416253"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="6da3c-102">エンティティの状態の操作</span><span class="sxs-lookup"><span data-stu-id="6da3c-102">Working with entity states</span></span>
<span data-ttu-id="6da3c-103">このトピックでは、コンテキストにエンティティを追加してアタッチする方法と、それらを SaveChanges 中に Entity Framework 処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="6da3c-104">Entity Framework は、コンテキストへの接続中にエンティティの状態を追跡しますが、切断されたシナリオや N 層のシナリオでは、エンティティの状態を EF に知らせることができます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="6da3c-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="6da3c-106">エンティティの状態と SaveChanges</span><span class="sxs-lookup"><span data-stu-id="6da3c-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="6da3c-107">エンティティは、EntityState 列挙で定義されている5つの状態のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="6da3c-108">これらの状態を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-108">These states are:</span></span>  

- <span data-ttu-id="6da3c-109">追加: エンティティはコンテキストによって追跡されていますが、データベースにまだ存在していません</span><span class="sxs-lookup"><span data-stu-id="6da3c-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="6da3c-110">Unchanged: エンティティがコンテキストによって追跡され、データベースに存在し、そのプロパティ値がデータベース内の値から変更されていません。</span><span class="sxs-lookup"><span data-stu-id="6da3c-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="6da3c-111">Modified: エンティティがコンテキストによって追跡され、データベースに存在し、そのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="6da3c-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="6da3c-112">Deleted: エンティティがコンテキストによって追跡されていて、データベースに存在するが、次回 SaveChanges が呼び出されたときにデータベースから削除対象としてマークされている</span><span class="sxs-lookup"><span data-stu-id="6da3c-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="6da3c-113">デタッチ済み: エンティティがコンテキストによって追跡されていません</span><span class="sxs-lookup"><span data-stu-id="6da3c-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="6da3c-114">SaveChanges は、さまざまな状態のエンティティに対してさまざまな処理を行います。</span><span class="sxs-lookup"><span data-stu-id="6da3c-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="6da3c-115">変更されていないエンティティは SaveChanges によって操作されません。</span><span class="sxs-lookup"><span data-stu-id="6da3c-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="6da3c-116">更新プログラムは、変更されていない状態のエンティティのデータベースには送信されません。</span><span class="sxs-lookup"><span data-stu-id="6da3c-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="6da3c-117">追加されたエンティティはデータベースに挿入され、SaveChanges が返されるときには変更されません。</span><span class="sxs-lookup"><span data-stu-id="6da3c-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="6da3c-118">変更されたエンティティはデータベースで更新され、SaveChanges が戻るときに変更されないようになります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="6da3c-119">削除されたエンティティはデータベースから削除され、その後コンテキストからデタッチされます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="6da3c-120">次の例は、エンティティまたはエンティティグラフの状態を変更できる方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6da3c-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="6da3c-121">コンテキストへの新しいエンティティの追加</span><span class="sxs-lookup"><span data-stu-id="6da3c-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="6da3c-122">DbSet で Add メソッドを呼び出すことによって、新しいエンティティをコンテキストに追加できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="6da3c-123">これにより、エンティティは追加された状態になります。つまり、次に SaveChanges が呼び出されたときにデータベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="6da3c-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-125">コンテキストに新しいエンティティを追加するもう1つの方法は、その状態を "追加" に変更することです。</span><span class="sxs-lookup"><span data-stu-id="6da3c-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="6da3c-126">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-127">最後に、既に追跡されている別のエンティティにフックすることによって、新しいエンティティをコンテキストに追加できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="6da3c-128">これは、別のエンティティのコレクションナビゲーションプロパティに新しいエンティティを追加するか、新しいエンティティを指す別のエンティティの参照ナビゲーションプロパティを設定することによって行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="6da3c-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-130">これらのすべての例では、追加するエンティティがまだ追跡されていない他のエンティティを参照している場合、これらの新しいエンティティもコンテキストに追加され、次に SaveChanges が呼び出されたときにデータベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="6da3c-131">既存のエンティティをコンテキストにアタッチする</span><span class="sxs-lookup"><span data-stu-id="6da3c-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="6da3c-132">データベースに既に存在しているが、現在コンテキストによって追跡されていないエンティティがある場合は、DbSet で Attach メソッドを使用してエンティティを追跡するようにコンテキストに指示できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="6da3c-133">エンティティは、コンテキスト内の変更されていない状態になります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="6da3c-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-135">アタッチされたエンティティに対して他の操作を行わずに SaveChanges を呼び出すと、データベースは変更されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6da3c-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="6da3c-136">これは、エンティティが変更されていない状態であるためです。</span><span class="sxs-lookup"><span data-stu-id="6da3c-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="6da3c-137">既存のエンティティをコンテキストにアタッチするもう1つの方法は、その状態を変更なしに変更することです。</span><span class="sxs-lookup"><span data-stu-id="6da3c-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="6da3c-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-139">これらの両方の例で、アタッチされているエンティティがまだ追跡されていない他のエンティティを参照している場合は、これらの新しいエンティティも変更されていない状態でコンテキストにアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="6da3c-140">既存の変更されたエンティティをコンテキストにアタッチする</span><span class="sxs-lookup"><span data-stu-id="6da3c-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="6da3c-141">データベースに既に存在し、変更が加えられていることがわかっているエンティティがある場合は、エンティティをアタッチするようにコンテキストに指示し、その状態を Modified に設定できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="6da3c-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="6da3c-143">状態を "変更済み" に変更すると、エンティティのすべてのプロパティが変更済みとしてマークされ、SaveChanges が呼び出されたときにすべてのプロパティ値がデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="6da3c-144">アタッチされているエンティティにまだ追跡されていない他のエンティティへの参照が含まれている場合、これらの新しいエンティティは、変更されていない状態でコンテキストにアタッチされるため、自動的に変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="6da3c-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="6da3c-145">複数のエンティティを変更済みとしてマークする必要がある場合は、これらの各エンティティの状態を個別に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="6da3c-146">追跡対象エンティティの状態の変更</span><span class="sxs-lookup"><span data-stu-id="6da3c-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="6da3c-147">エントリの状態プロパティを設定することによって、既に追跡されているエンティティの状態を変更できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="6da3c-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-148">For example:</span></span>  

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

<span data-ttu-id="6da3c-149">既に追跡されているエンティティに対して追加またはアタッチを呼び出すと、エンティティの状態を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="6da3c-150">たとえば、現在追加されているエンティティの Attach を呼び出すと、その状態が Unchanged に変わります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="6da3c-151">パターンの挿入または更新</span><span class="sxs-lookup"><span data-stu-id="6da3c-151">Insert or update pattern</span></span>  

<span data-ttu-id="6da3c-152">アプリケーションによっては、主キーの値に応じて、エンティティを新規として追加するか (データベースの挿入を結果として)、エンティティを既存のものとしてアタッチし、変更済みとしてマークする (データベースの更新を作成する) ことがあります。</span><span class="sxs-lookup"><span data-stu-id="6da3c-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="6da3c-153">たとえば、データベースによって生成された整数の主キーを使用する場合は、エンティティを new として、0以外のキーを持つエンティティを既存のエンティティとして扱うのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="6da3c-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="6da3c-154">このパターンは、主キーの値のチェックに基づいてエンティティの状態を設定することによって実現できます。</span><span class="sxs-lookup"><span data-stu-id="6da3c-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="6da3c-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6da3c-155">For example:</span></span>  

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

<span data-ttu-id="6da3c-156">状態を "変更済み" に変更すると、エンティティのすべてのプロパティが変更済みとしてマークされ、SaveChanges が呼び出されたときにすべてのプロパティ値がデータベースに送信されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6da3c-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
