---
title: EF6 の Load メソッド
author: divega
ms.date: 2016-10-23
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: f7e8410b8fb8b5c3e86c51cd61868604a7566d0c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996652"
---
# <a name="the-load-method"></a><span data-ttu-id="8167d-102">Load メソッド</span><span class="sxs-lookup"><span data-stu-id="8167d-102">The Load Method</span></span>
<span data-ttu-id="8167d-103">それらのエンティティにすぐに何もせず、データベースからのコンテキストにエンティティを読み込む必要のあるいくつかのシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="8167d-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="8167d-104">これの良い例」の説明に従ってデータ バインディングのエンティティの読み込みが[ローカル データ](~/ef6/querying/local-data.md)します。</span><span class="sxs-lookup"><span data-stu-id="8167d-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="8167d-105">これを行う 1 つの一般的な方法は、LINQ クエリを記述し、それを作成するリストを直ちに破棄するだけで ToList を呼び出すです。</span><span class="sxs-lookup"><span data-stu-id="8167d-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="8167d-106">Load 拡張メソッドは、完全リストの作成を回避する点を除いて、ToList と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="8167d-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="8167d-107">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="8167d-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="8167d-108">負荷を使用して 2 つの例を示します。</span><span class="sxs-lookup"><span data-stu-id="8167d-108">Here are two examples of using Load.</span></span> <span data-ttu-id="8167d-109">最初は負荷が使用されている Windows フォーム データ バインド アプリケーションから取得」の説明に従って、ローカル コレクションにバインドする前にエンティティを照会する[ローカル データ](~/ef6/querying/local-data.md):</span><span class="sxs-lookup"><span data-stu-id="8167d-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="8167d-110">」の説明に従って、関連エンティティのフィルター選択されたコレクションを読み込むロードを使用して、2 番目の例に示す[関連エンティティの読み込み](~/ef6/querying/related-data.md):</span><span class="sxs-lookup"><span data-stu-id="8167d-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  
