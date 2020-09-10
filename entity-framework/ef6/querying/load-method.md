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
# <a name="the-load-method"></a>Load メソッド
いくつかのシナリオでは、エンティティをすぐに実行することなく、データベースからコンテキストにエンティティを読み込むことができます。 この例では、「 [ローカルデータ](xref:ef6/querying/local-data)」で説明されているように、データバインディングのエンティティを読み込んでいます。 これを行う一般的な方法の1つは、LINQ クエリを記述し、そのクエリで ToList を呼び出して、作成されたリストを直ちに破棄することです。 Load 拡張メソッドは、リストの作成を完全に回避することを除けば、ToList と同じように動作します。  

このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

Load を使用する2つの例を次に示します。 1つ目は、「 [ローカルデータ](xref:ef6/querying/local-data):」で説明されているように、ローカルコレクションにバインドする前に、読み込みを使用してエンティティを照会する Windows フォームデータバインディングアプリケーションから取得されます。  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

2番目の例では、「 [関連エンティティの読み込み](xref:ef6/querying/related-data)」で説明されているように、負荷を使用して、フィルター処理された関連エンティティのコレクションを読み込みます。  

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
