---
title: 同時実行の競合の処理-EF6
description: Entity Framework 6 での同時実行の競合の処理
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/concurrency
ms.openlocfilehash: 0cec285ab6071120e162567506d397a23c299177
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064511"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="870ba-103">同時実行の競合の処理 (EF6)</span><span class="sxs-lookup"><span data-stu-id="870ba-103">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="870ba-104">オプティミスティック同時実行制御では、エンティティが読み込まれてからデータが変更されていないことを期待して、エンティティをデータベースに保存しようとしています。</span><span class="sxs-lookup"><span data-stu-id="870ba-104">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="870ba-105">データが変更されたことが判明した場合は、例外がスローされるので、再度保存する前に競合を解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="870ba-105">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="870ba-106">このトピックでは、Entity Framework でこのような例外を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="870ba-106">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="870ba-107">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="870ba-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="870ba-108">この投稿は、オプティミスティック同時実行制御を完全に説明するための適切な場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="870ba-108">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="870ba-109">以下のセクションでは、同時実行の解決についての知識を前提とし、一般的なタスクのパターンを示します。</span><span class="sxs-lookup"><span data-stu-id="870ba-109">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="870ba-110">これらのパターンの多くは、「 [プロパティ値の操作](xref:ef6/saving/change-tracking/property-values)」で説明されているトピックを使用します。</span><span class="sxs-lookup"><span data-stu-id="870ba-110">Many of these patterns make use of the topics discussed in [Working with Property Values](xref:ef6/saving/change-tracking/property-values).</span></span>  

<span data-ttu-id="870ba-111">独立した関連付け (外部キーがエンティティのプロパティにマップされていない場合) を使用する場合の同時実行の問題の解決は、外部キーの関連付けを使用する場合よりもはるかに困難です。</span><span class="sxs-lookup"><span data-stu-id="870ba-111">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="870ba-112">したがって、アプリケーションで同時実行の解決を行う場合は、常に外部キーをエンティティにマップすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="870ba-112">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="870ba-113">以下のすべての例では、外部キーの関連付けを使用していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="870ba-113">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="870ba-114">外部キーの関連付けを使用するエンティティを保存しようとしているときに、オプティミスティック同時実行制御の例外が検出されると、DbUpdateConcurrencyException が SaveChanges によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="870ba-114">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="870ba-115">再読み込みによるオプティミスティック同時実行例外の解決 (データベースの優先)</span><span class="sxs-lookup"><span data-stu-id="870ba-115">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="870ba-116">再読み込みメソッドを使用して、エンティティの現在の値をデータベース内の現在の値で上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="870ba-116">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="870ba-117">エンティティは通常、何らかの形でユーザーに戻され、再度変更を加えて再保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="870ba-117">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="870ba-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="870ba-118">For example:</span></span>  

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

<span data-ttu-id="870ba-119">同時実行例外をシミュレートするには、SaveChanges 呼び出しにブレークポイントを設定し、SQL Server Management Studio などの別のツールを使用してデータベースに保存されているエンティティを変更する方法が適しています。</span><span class="sxs-lookup"><span data-stu-id="870ba-119">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="870ba-120">SaveChanges の前に行を挿入して、SqlCommand を使用してデータベースを直接更新することもできます。</span><span class="sxs-lookup"><span data-stu-id="870ba-120">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="870ba-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="870ba-121">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="870ba-122">DbUpdateConcurrencyException の Entries メソッドは、更新に失敗したエンティティの DbEntityEntry インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="870ba-122">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="870ba-123">(現在、このプロパティは、同時実行の問題に対して常に単一の値を返します。</span><span class="sxs-lookup"><span data-stu-id="870ba-123">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="870ba-124">一般的な更新例外に対して複数の値が返される場合があります)。場合によっては、データベースからの再読み込みが必要になる可能性のあるすべてのエンティティのエントリを取得し、それぞれに対して再読み込みを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="870ba-124">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="870ba-125">クライアント優先としてのオプティミスティック同時実行例外の解決</span><span class="sxs-lookup"><span data-stu-id="870ba-125">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="870ba-126">再読み込みを使用する上記の例は、データベースの値によってエンティティ内の値が上書きされるため、データベースの優先またはストアの優先と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="870ba-126">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="870ba-127">場合によっては、逆の処理を行い、現在エンティティ内にある値を使用してデータベースの値を上書きすることが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="870ba-127">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="870ba-128">これは、クライアント優先とも呼ばれ、現在のデータベース値を取得し、エンティティの元の値として設定することによって実行できます。</span><span class="sxs-lookup"><span data-stu-id="870ba-128">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="870ba-129">(現在の値と元の値の詳細については、「 [プロパティ値の使用](xref:ef6/saving/change-tracking/property-values) 」を参照してください)。例えば：</span><span class="sxs-lookup"><span data-stu-id="870ba-129">(See [Working with Property Values](xref:ef6/saving/change-tracking/property-values) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="870ba-130">オプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="870ba-130">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="870ba-131">場合によっては、現在データベース内にある値を、現在エンティティ内にある値と組み合わせる必要があります。</span><span class="sxs-lookup"><span data-stu-id="870ba-131">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="870ba-132">これには通常、カスタムロジックまたはユーザーの操作が必要です。</span><span class="sxs-lookup"><span data-stu-id="870ba-132">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="870ba-133">たとえば、現在の値、データベース内の値、および解決済みの値の既定のセットを含むフォームをユーザーに提示することができます。</span><span class="sxs-lookup"><span data-stu-id="870ba-133">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="870ba-134">その後、ユーザーは必要に応じて解決済みの値を編集し、データベースに保存される解決済みの値になります。</span><span class="sxs-lookup"><span data-stu-id="870ba-134">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="870ba-135">これは、エンティティのエントリの CurrentValues および GetDatabaseValues から返された DbPropertyValues オブジェクトを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="870ba-135">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="870ba-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="870ba-136">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="870ba-137">オブジェクトを使用したオプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="870ba-137">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="870ba-138">上記のコードでは、DbPropertyValues インスタンスを使用して、現在、データベース、および解決済みの値を渡しています。</span><span class="sxs-lookup"><span data-stu-id="870ba-138">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="870ba-139">このために、エンティティ型のインスタンスを使用する方が簡単な場合があります。</span><span class="sxs-lookup"><span data-stu-id="870ba-139">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="870ba-140">これは、DbPropertyValues の ToObject および SetValues メソッドを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="870ba-140">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="870ba-141">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="870ba-141">For example:</span></span>  

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
