---
title: Load メソッド-EF6
description: Entity Framework 6 の Load メソッド
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
uid: ef6/querying/load-method
ms.openlocfilehash: 5fbb55b899ae0ee026d42df90f80cc18fe95bfa2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620323"
---
# <a name="the-load-method"></a><span data-ttu-id="0d3d8-103">Load メソッド</span><span class="sxs-lookup"><span data-stu-id="0d3d8-103">The Load Method</span></span>
<span data-ttu-id="0d3d8-104">いくつかのシナリオでは、エンティティをすぐに実行することなく、データベースからコンテキストにエンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-104">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="0d3d8-105">この例では、「 [ローカルデータ](xref:ef6/querying/local-data)」で説明されているように、データバインディングのエンティティを読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-105">A good example of this is loading entities for data binding as described in [Local Data](xref:ef6/querying/local-data).</span></span> <span data-ttu-id="0d3d8-106">これを行う一般的な方法の1つは、LINQ クエリを記述し、そのクエリで ToList を呼び出して、作成されたリストを直ちに破棄することです。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-106">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="0d3d8-107">Load 拡張メソッドは、リストの作成を完全に回避することを除けば、ToList と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-107">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="0d3d8-108">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-108">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="0d3d8-109">Load を使用する2つの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-109">Here are two examples of using Load.</span></span> <span data-ttu-id="0d3d8-110">1つ目は、「 [ローカルデータ](xref:ef6/querying/local-data):」で説明されているように、ローカルコレクションにバインドする前に、読み込みを使用してエンティティを照会する Windows フォームデータバインディングアプリケーションから取得されます。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-110">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](xref:ef6/querying/local-data):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="0d3d8-111">2番目の例では、「 [関連エンティティの読み込み](xref:ef6/querying/related-data)」で説明されているように、負荷を使用して、フィルター処理された関連エンティティのコレクションを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0d3d8-111">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](xref:ef6/querying/related-data):</span></span>  

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
