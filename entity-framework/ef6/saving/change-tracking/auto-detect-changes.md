---
title: 自動検出の EF6 の変更
author: divega
ms.date: 2016-10-23
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: bca33e12674c47cc7e047e85b11746c8e39246b4
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998100"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="1770c-102">自動の変更を検出します。</span><span class="sxs-lookup"><span data-stu-id="1770c-102">Automatic detect changes</span></span>
<span data-ttu-id="1770c-103">ほとんどの POCO エンティティを使用する場合は、エンティティがどのように変更されたか (および、したがって、更新プログラムがデータベースに送信する必要があります) の決定が、変更の検出アルゴリズムによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="1770c-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="1770c-104">エンティティの現在のプロパティ値と、エンティティが照会したり、接続されているときに、スナップショットに格納されている元のプロパティ値の違いを検出することによって動作の変更を検出します。</span><span class="sxs-lookup"><span data-stu-id="1770c-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="1770c-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="1770c-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="1770c-106">既定では、Entity Framework は、次のメソッドが呼び出されたときに自動的に変更の検出を実行します。</span><span class="sxs-lookup"><span data-stu-id="1770c-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="1770c-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="1770c-107">DbSet.Find</span></span>  
- <span data-ttu-id="1770c-108">DbSet.Local</span><span class="sxs-lookup"><span data-stu-id="1770c-108">DbSet.Local</span></span>  
- <span data-ttu-id="1770c-109">DbSet.Add</span><span class="sxs-lookup"><span data-stu-id="1770c-109">DbSet.Add</span></span>  
- <span data-ttu-id="1770c-110">DbSet.AddRange</span><span class="sxs-lookup"><span data-stu-id="1770c-110">DbSet.AddRange</span></span>
- <span data-ttu-id="1770c-111">DbSet.Remove</span><span class="sxs-lookup"><span data-stu-id="1770c-111">DbSet.Remove</span></span>  
- <span data-ttu-id="1770c-112">DbSet.RemoveRange</span><span class="sxs-lookup"><span data-stu-id="1770c-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="1770c-113">DbSet.Attach</span><span class="sxs-lookup"><span data-stu-id="1770c-113">DbSet.Attach</span></span>  
- <span data-ttu-id="1770c-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="1770c-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="1770c-115">DbContext.GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="1770c-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="1770c-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="1770c-116">DbContext.Entry</span></span>  
- <span data-ttu-id="1770c-117">DbChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="1770c-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="1770c-118">変更の自動検出を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1770c-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="1770c-119">コンテキストで大量のエンティティを追跡する、ループ内で何度もがこれらのメソッドの 1 つ呼び出すと、ループの実行中の変更の検出を無効にすることで大幅なパフォーマンス向上を取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1770c-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="1770c-120">例えば:</span><span class="sxs-lookup"><span data-stu-id="1770c-120">For example:</span></span>  

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

<span data-ttu-id="1770c-121">再度、ループの後に変更の検出を有効にすることを忘れないでください-有効になっていることが常に再場合でも、ループのコードが例外をスローする try/finally 使用しました。</span><span class="sxs-lookup"><span data-stu-id="1770c-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="1770c-122">代わりに無効にすることはすべて時間といずれかの呼び出しコンテキストをオフになっている変更の自動検出のままに再度有効にするとします。ChangeTracker.DetectChanges 明示的にまたは鋭意変更追跡プロキシを使用します。</span><span class="sxs-lookup"><span data-stu-id="1770c-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="1770c-123">高度なし、をアプリケーションに微妙なバグを持ち込んでできます簡単にこれらのオプションの両方を慎重に使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="1770c-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="1770c-124">追加またはコンテキストから多くのオブジェクトを削除する必要がある場合は、DbSet.AddRange と DbSet.RemoveRange を使用して検討してください。</span><span class="sxs-lookup"><span data-stu-id="1770c-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="1770c-125">自動的に、このメソッドは 1 回だけ変更を検出し、追加または削除操作が完了した後。</span><span class="sxs-lookup"><span data-stu-id="1770c-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
