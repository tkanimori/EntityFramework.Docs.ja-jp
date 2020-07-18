---
title: 同時実行の競合の処理-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 4d29fd7a4d9b6003f71bc8411cea2d863a4c5429
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451243"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="4d2e0-102">同時実行の競合の処理 (EF6)</span><span class="sxs-lookup"><span data-stu-id="4d2e0-102">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="4d2e0-103">オプティミスティック同時実行制御では、エンティティが読み込まれてからデータが変更されていないことを期待して、エンティティをデータベースに保存しようとしています。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="4d2e0-104">データが変更されたことが判明した場合は、例外がスローされるので、再度保存する前に競合を解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="4d2e0-105">このトピックでは、Entity Framework でこのような例外を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="4d2e0-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="4d2e0-107">この投稿は、オプティミスティック同時実行制御を完全に説明するための適切な場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="4d2e0-108">以下のセクションでは、同時実行の解決についての知識を前提とし、一般的なタスクのパターンを示します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="4d2e0-109">これらのパターンの多くは、「[プロパティ値の操作](~/ef6/saving/change-tracking/property-values.md)」で説明されているトピックを使用します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="4d2e0-110">独立した関連付け (外部キーがエンティティのプロパティにマップされていない場合) を使用する場合の同時実行の問題の解決は、外部キーの関連付けを使用する場合よりもはるかに困難です。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="4d2e0-111">したがって、アプリケーションで同時実行の解決を行う場合は、常に外部キーをエンティティにマップすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="4d2e0-112">以下のすべての例では、外部キーの関連付けを使用していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="4d2e0-113">外部キーの関連付けを使用するエンティティを保存しようとしているときに、オプティミスティック同時実行制御の例外が検出されると、DbUpdateConcurrencyException が SaveChanges によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="4d2e0-114">再読み込みによるオプティミスティック同時実行例外の解決 (データベースの優先)</span><span class="sxs-lookup"><span data-stu-id="4d2e0-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="4d2e0-115">再読み込みメソッドを使用して、エンティティの現在の値をデータベース内の現在の値で上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="4d2e0-116">エンティティは通常、何らかの形でユーザーに戻され、再度変更を加えて再保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="4d2e0-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-117">For example:</span></span>  

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

<span data-ttu-id="4d2e0-118">同時実行例外をシミュレートするには、SaveChanges 呼び出しにブレークポイントを設定し、SQL Server Management Studio などの別のツールを使用してデータベースに保存されているエンティティを変更する方法が適しています。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="4d2e0-119">SaveChanges の前に行を挿入して、SqlCommand を使用してデータベースを直接更新することもできます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="4d2e0-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="4d2e0-121">DbUpdateConcurrencyException の Entries メソッドは、更新に失敗したエンティティの DbEntityEntry インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="4d2e0-122">(現在、このプロパティは、同時実行の問題に対して常に単一の値を返します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="4d2e0-123">一般的な更新例外に対して複数の値が返される場合があります)。場合によっては、データベースからの再読み込みが必要になる可能性のあるすべてのエンティティのエントリを取得し、それぞれに対して再読み込みを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="4d2e0-124">クライアント優先としてのオプティミスティック同時実行例外の解決</span><span class="sxs-lookup"><span data-stu-id="4d2e0-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="4d2e0-125">再読み込みを使用する上記の例は、データベースの値によってエンティティ内の値が上書きされるため、データベースの優先またはストアの優先と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="4d2e0-126">場合によっては、逆の処理を行い、現在エンティティ内にある値を使用してデータベースの値を上書きすることが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="4d2e0-127">これは、クライアント優先とも呼ばれ、現在のデータベース値を取得し、エンティティの元の値として設定することによって実行できます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="4d2e0-128">(現在の値と元の値の詳細については、「[プロパティ値の使用](~/ef6/saving/change-tracking/property-values.md)」を参照してください)。例えば：</span><span class="sxs-lookup"><span data-stu-id="4d2e0-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="4d2e0-129">オプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="4d2e0-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="4d2e0-130">場合によっては、現在データベース内にある値を、現在エンティティ内にある値と組み合わせる必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="4d2e0-131">これには通常、カスタムロジックまたはユーザーの操作が必要です。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="4d2e0-132">たとえば、現在の値、データベース内の値、および解決済みの値の既定のセットを含むフォームをユーザーに提示することができます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="4d2e0-133">その後、ユーザーは必要に応じて解決済みの値を編集し、データベースに保存される解決済みの値になります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="4d2e0-134">これは、エンティティのエントリの CurrentValues および GetDatabaseValues から返された DbPropertyValues オブジェクトを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="4d2e0-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-135">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="4d2e0-136">オブジェクトを使用したオプティミスティック同時実行例外のカスタム解決</span><span class="sxs-lookup"><span data-stu-id="4d2e0-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="4d2e0-137">上記のコードでは、DbPropertyValues インスタンスを使用して、現在、データベース、および解決済みの値を渡しています。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="4d2e0-138">このために、エンティティ型のインスタンスを使用する方が簡単な場合があります。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="4d2e0-139">これは、DbPropertyValues の ToObject および SetValues メソッドを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="4d2e0-140">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4d2e0-140">For example:</span></span>  

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
