---
title: 同時実行の競合の EF6 の処理
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
caps.latest.revision: 3
ms.openlocfilehash: b8608dbb4cadd60ff4ff4984583f8a9d843b3949
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121835"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="a9d9a-102">同時実行の競合の処理</span><span class="sxs-lookup"><span data-stu-id="a9d9a-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="a9d9a-103">オプティミスティック同時実行は共有的では、エンティティをエンティティからデータが変更されていないことを期待してデータベースに保存しようとしてが読み込まれました。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="a9d9a-104">わかった場合、例外がスローされ、再度保存する前に、競合を解決する必要がありますし、データが変更されました。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="a9d9a-105">このトピックでは、Entity Framework では、このような例外を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="a9d9a-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="a9d9a-107">この投稿はオプティミスティック同時実行制御の詳細については、適切な場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="a9d9a-108">以下のセクションでは、同時実行の解像度の知識を前提としていて、一般的なタスクのパターンを表示します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="a9d9a-109">これらのパターンの多くで説明したトピックの使用[プロパティの値を操作](~/ef6/saving/change-tracking/property-values.md)します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="a9d9a-110">使用している独立した関連付け (場所、外部キーにマップされていないエンティティのプロパティ) と、同時実行の問題を解決するは外部キー アソシエーションを使用する場合よりもはるかに難しくなります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="a9d9a-111">そのため、アプリケーションで同時実行の解決を行う場合に、常に、エンティティに外部キーをマップすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="a9d9a-112">次の例では、外部キー アソシエーションを使用していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="a9d9a-113">外部キー アソシエーションを使用するエンティティを保存しようとしているときに、オプティミスティック同時実行例外が検出されたときに、DbUpdateConcurrencyException が SaveChanges によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="a9d9a-114">再読み込み (データベース wins) とオプティミスティック同時実行例外を解決します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="a9d9a-115">データベースの値で、エンティティの現在の値を上書きする Reload メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="a9d9a-116">エンティティは通常に渡され、戻るなんらかの形でユーザーと、もう一度その変更を加えるし、再度保存しようとする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="a9d9a-117">例えば:</span><span class="sxs-lookup"><span data-stu-id="a9d9a-117">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="a9d9a-118">同時実行例外をシミュレートするためには、SaveChanges の呼び出しにブレークポイントを設定し、SQL Management Studio などの他のツールを使用してデータベースに保存されているエンティティを変更します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Management Studio.</span></span> <span data-ttu-id="a9d9a-119">SaveChanges SqlCommand を直接使用してデータベースを更新する前に行を挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="a9d9a-120">例えば:</span><span class="sxs-lookup"><span data-stu-id="a9d9a-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="a9d9a-121">DbUpdateConcurrencyException のエントリ メソッドでは、更新に失敗したエンティティの対象の DbEntityEntry インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="a9d9a-122">(現在常に返します同時実行の問題の 1 つの値。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="a9d9a-123">これは値を返す複数の一般的な更新プログラムの例外の。)これらの各呼び出しが再読み込みやデータベースから再読み込みする必要があるすべてのエンティティのエントリを取得する代わりに適さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="a9d9a-124">クライアント側に合わせると、オプティミスティック同時実行例外を解決します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="a9d9a-125">再読み込みを使用する上記の例が、データベースの wins とも呼ばれます。 または store wins エンティティ内の値がデータベースからの値によって上書きされるためです。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="a9d9a-126">逆を行うし、エンティティの現在の値で、データベース内の値を上書きするたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="a9d9a-127">これは、クライアント側に合わせるとも呼ばれますが、データベースの現在の値を取得して、エンティティの元の値として設定して実行できます。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="a9d9a-128">(を参照してください[プロパティの値を操作](~/ef6/saving/change-tracking/property-values.md)現在と元の値の詳細について)。例えば:</span><span class="sxs-lookup"><span data-stu-id="a9d9a-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="a9d9a-129">オプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="a9d9a-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="a9d9a-130">場合によって、エンティティの現在の値と、データベースの現在の値を組み合わせるしたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="a9d9a-131">これには、カスタム ロジックやユーザー操作がいくつかは、通常必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="a9d9a-132">たとえば、フォームを現在の値は、データベース内の値を格納しているユーザーに表示可能性があり、既定値に解決される値の設定。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="a9d9a-133">ユーザーは必要に応じて、解決された値を編集し、データベースに保存されるこれらの解決された値になります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="a9d9a-134">これ行うエンティティのエントリで CurrentValues とではから返される DbPropertyValues オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="a9d9a-135">例えば:</span><span class="sxs-lookup"><span data-stu-id="a9d9a-135">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="a9d9a-136">オブジェクトを使用してオプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="a9d9a-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="a9d9a-137">上記のコードでは、現在を受け渡す、データベース、および解決される値の DbPropertyValues インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="a9d9a-138">このエンティティ型のインスタンスを使いやすい場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="a9d9a-139">これ行う DbPropertyValues の ToObject や SetValues メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9d9a-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="a9d9a-140">例えば:</span><span class="sxs-lookup"><span data-stu-id="a9d9a-140">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
