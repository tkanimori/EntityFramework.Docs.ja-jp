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
# <a name="automatic-detect-changes"></a>自動の変更を検出します。
ほとんどの POCO エンティティを使用する場合は、エンティティがどのように変更されたか (および、したがって、更新プログラムがデータベースに送信する必要があります) の決定が、変更の検出アルゴリズムによって処理されます。 エンティティの現在のプロパティ値と、エンティティが照会したり、接続されているときに、スナップショットに格納されている元のプロパティ値の違いを検出することによって動作の変更を検出します。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

既定では、Entity Framework は、次のメソッドが呼び出されたときに自動的に変更の検出を実行します。  

- DbSet.Find  
- DbSet.Local  
- DbSet.Add  
- DbSet.AddRange
- DbSet.Remove  
- DbSet.RemoveRange
- DbSet.Attach  
- DbContext.SaveChanges  
- DbContext.GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker.Entries  

## <a name="disabling-automatic-detection-of-changes"></a>変更の自動検出を無効にします。  

コンテキストで大量のエンティティを追跡する、ループ内で何度もがこれらのメソッドの 1 つ呼び出すと、ループの実行中の変更の検出を無効にすることで大幅なパフォーマンス向上を取得可能性があります。 例えば:  

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

再度、ループの後に変更の検出を有効にすることを忘れないでください-有効になっていることが常に再場合でも、ループのコードが例外をスローする try/finally 使用しました。  

代わりに無効にすることはすべて時間といずれかの呼び出しコンテキストをオフになっている変更の自動検出のままに再度有効にするとします。ChangeTracker.DetectChanges 明示的にまたは鋭意変更追跡プロキシを使用します。 高度なし、をアプリケーションに微妙なバグを持ち込んでできます簡単にこれらのオプションの両方を慎重に使用するようにします。  

追加またはコンテキストから多くのオブジェクトを削除する必要がある場合は、DbSet.AddRange と DbSet.RemoveRange を使用して検討してください。 自動的に、このメソッドは 1 回だけ変更を検出し、追加または削除操作が完了した後。 
