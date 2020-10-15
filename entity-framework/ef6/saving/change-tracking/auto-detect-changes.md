---
title: 自動検出変更-EF6
description: Entity Framework 6 での変更の自動検出
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 159143a9ade64a65e857a30117e577b21e0b9f98
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064497"
---
# <a name="automatic-detect-changes"></a>自動検出の変更
ほとんどの POCO エンティティを使用する場合、エンティティがどのように変更されたか (そのため、どの更新をデータベースに送信する必要があるか) は、[変更の検出] アルゴリズムによって処理されます。 変更の検出は、エンティティの現在のプロパティ値と、エンティティがクエリまたはアタッチされたときにスナップショットに格納されている元のプロパティ値との違いを検出することによって機能します。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

既定では、次のメソッドが呼び出されると、Entity Framework によって変更が自動的に検出されます。  

- DbSet.Find  
- DbSet. ローカル  
- DbSet。追加  
- DbSet. AddRange
- DbSet。削除  
- DbSet. RemoveRange 設定
- DbSet. Attach  
- DbContext.SaveChanges  
- DbContext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker。エントリ  

## <a name="disabling-automatic-detection-of-changes"></a>変更の自動検出を無効にする  

コンテキストで多数のエンティティを追跡していて、ループ内でこれらのメソッドのいずれかを何度も呼び出す場合、ループの実行中に変更の検出をオフにすることで、パフォーマンスを大幅に向上させることができます。 次に例を示します。  

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

ループの後で変更の検出を再度有効にすることを忘れないでください。 try/finally を使用して、ループ内のコードが例外をスローした場合でも、常に再有効化されるようにしました。  

無効化と再有効化の代替手段として、変更の自動検出を常に無効にして、コンテキストを呼び出すことができます。ChangeTracker は、明示的に変更するか、変更追跡プロキシを入念に使用します。 これらのオプションはどちらも高度であり、微妙なバグをアプリケーションに簡単に導入できるため、慎重に使用してください。  

コンテキストから多数のオブジェクトを追加または削除する必要がある場合は、DbSet. AddRange と DbSet. RemoveRange 使用を検討してください。 このメソッドは、追加操作または削除操作が完了した後に、変更を自動的に検出します。 
