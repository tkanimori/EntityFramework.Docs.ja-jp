---
title: 自動検出変更-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414381"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="3c274-102">自動検出の変更</span><span class="sxs-lookup"><span data-stu-id="3c274-102">Automatic detect changes</span></span>
<span data-ttu-id="3c274-103">ほとんどの POCO エンティティを使用する場合、エンティティがどのように変更されたか (そのため、どの更新をデータベースに送信する必要があるか) は、[変更の検出] アルゴリズムによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="3c274-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="3c274-104">変更の検出は、エンティティの現在のプロパティ値と、エンティティがクエリまたはアタッチされたときにスナップショットに格納されている元のプロパティ値との違いを検出することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="3c274-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="3c274-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c274-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="3c274-106">既定では、次のメソッドが呼び出されると、Entity Framework によって変更が自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="3c274-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="3c274-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="3c274-107">DbSet.Find</span></span>  
- <span data-ttu-id="3c274-108">DbSet. ローカル</span><span class="sxs-lookup"><span data-stu-id="3c274-108">DbSet.Local</span></span>  
- <span data-ttu-id="3c274-109">DbSet。追加</span><span class="sxs-lookup"><span data-stu-id="3c274-109">DbSet.Add</span></span>  
- <span data-ttu-id="3c274-110">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="3c274-110">DbSet.AddRange</span></span>
- <span data-ttu-id="3c274-111">DbSet。削除</span><span class="sxs-lookup"><span data-stu-id="3c274-111">DbSet.Remove</span></span>  
- <span data-ttu-id="3c274-112">DbSet. RemoveRange 設定</span><span class="sxs-lookup"><span data-stu-id="3c274-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="3c274-113">DbSet. Attach</span><span class="sxs-lookup"><span data-stu-id="3c274-113">DbSet.Attach</span></span>  
- <span data-ttu-id="3c274-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="3c274-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="3c274-115">DbContext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="3c274-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="3c274-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="3c274-116">DbContext.Entry</span></span>  
- <span data-ttu-id="3c274-117">DbChangeTracker。エントリ</span><span class="sxs-lookup"><span data-stu-id="3c274-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="3c274-118">変更の自動検出を無効にする</span><span class="sxs-lookup"><span data-stu-id="3c274-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="3c274-119">コンテキストで多数のエンティティを追跡していて、ループ内でこれらのメソッドのいずれかを何度も呼び出す場合、ループの実行中に変更の検出をオフにすることで、パフォーマンスを大幅に向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="3c274-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="3c274-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3c274-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

<span data-ttu-id="3c274-121">ループの後で変更の検出を再度有効にすることを忘れないでください。 try/finally を使用して、ループ内のコードが例外をスローした場合でも、常に再有効化されるようにしました。</span><span class="sxs-lookup"><span data-stu-id="3c274-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="3c274-122">無効化と再有効化の代替手段として、変更の自動検出を常に無効にして、コンテキストを呼び出すことができます。ChangeTracker は、明示的に変更するか、変更追跡プロキシを入念に使用します。</span><span class="sxs-lookup"><span data-stu-id="3c274-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="3c274-123">これらのオプションはどちらも高度であり、微妙なバグをアプリケーションに簡単に導入できるため、慎重に使用してください。</span><span class="sxs-lookup"><span data-stu-id="3c274-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="3c274-124">コンテキストから多数のオブジェクトを追加または削除する必要がある場合は、DbSet. AddRange と DbSet. RemoveRange 使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="3c274-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="3c274-125">このメソッドは、追加操作または削除操作が完了した後に、変更を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="3c274-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
