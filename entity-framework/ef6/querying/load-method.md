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
# <a name="the-load-method"></a>Load メソッド
いくつかのシナリオでは、エンティティをすぐに実行することなく、データベースからコンテキストにエンティティを読み込むことができます。 この例では、「[ローカルデータ](~/ef6/querying/local-data.md)」で説明されているように、データバインディングのエンティティを読み込んでいます。 これを行う一般的な方法の1つは、LINQ クエリを記述し、そのクエリで ToList を呼び出して、作成されたリストを直ちに破棄することです。 Load 拡張メソッドは、リストの作成を完全に回避することを除けば、ToList と同じように動作します。  

このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

Load を使用する2つの例を次に示します。 1つ目は、「[ローカルデータ](~/ef6/querying/local-data.md):」で説明されているように、ローカルコレクションにバインドする前に、読み込みを使用してエンティティを照会する Windows フォームデータバインディングアプリケーションから取得されます。  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

2番目の例では、「[関連エンティティの読み込み](~/ef6/querying/related-data.md)」で説明されているように、負荷を使用して、フィルター処理された関連エンティティのコレクションを読み込みます。  

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
