---
title: Load メソッド-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: bcea8ab2477f44281cd5de824457a72a84ccc766
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414495"
---
# <a name="the-load-method"></a><span data-ttu-id="2de6c-102">Load メソッド</span><span class="sxs-lookup"><span data-stu-id="2de6c-102">The Load Method</span></span>
<span data-ttu-id="2de6c-103">いくつかのシナリオでは、エンティティをすぐに実行することなく、データベースからコンテキストにエンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2de6c-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="2de6c-104">この例では、「[ローカルデータ](~/ef6/querying/local-data.md)」で説明されているように、データバインディングのエンティティを読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="2de6c-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="2de6c-105">これを行う一般的な方法の1つは、LINQ クエリを記述し、そのクエリで ToList を呼び出して、作成されたリストを直ちに破棄することです。</span><span class="sxs-lookup"><span data-stu-id="2de6c-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="2de6c-106">Load 拡張メソッドは、リストの作成を完全に回避することを除けば、ToList と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="2de6c-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="2de6c-107">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="2de6c-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="2de6c-108">Load を使用する2つの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2de6c-108">Here are two examples of using Load.</span></span> <span data-ttu-id="2de6c-109">1つ目は、「[ローカルデータ](~/ef6/querying/local-data.md):」で説明されているように、ローカルコレクションにバインドする前に、読み込みを使用してエンティティを照会する Windows フォームデータバインディングアプリケーションから取得されます。</span><span class="sxs-lookup"><span data-stu-id="2de6c-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="2de6c-110">2番目の例では、「[関連エンティティの読み込み](~/ef6/querying/related-data.md)」で説明されているように、負荷を使用して、フィルター処理された関連エンティティのコレクションを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2de6c-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
