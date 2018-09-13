---
title: エンティティの状態 - EF6 の使用
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: c1dde7810d1dfa8a73e6bd2cf091b24be6b865d8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490666"
---
# <a name="working-with-entity-states"></a>エンティティの状態の操作
このトピックでは、追加し、コンテキストにエンティティを接続する方法、および Entity Framework が SaveChanges 中にこれらを処理する方法について説明します。
Entity Framework で、コンテキストに接続されている、または未接続の N 層シナリオでは EF にどのような状態のエンティティを通知させることができますが、エンティティの状態であるか追跡処理されます。
このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="entity-states-and-savechanges"></a>エンティティの状態および SaveChanges

EntityState 列挙体によって定義されている 5 つの状態のいずれかでエンティティができます。 これらの状態は次のとおりです。  

- : 追加されたエンティティがコンテキストによって追跡されているが、データベースに存在しません  
- Unchanged: エンティティがコンテキストによって追跡されていると、データベースに存在して、プロパティの値が、データベース内の値から変更されていません  
- 更新日時: エンティティがコンテキストによって追跡されていると、データベースに存在して、プロパティの値の一部またはすべてが変更されました。  
- 削除: エンティティがコンテキストによって追跡されていると、データベースに存在しますが、次回に SaveChanges が呼び出されたとき、データベースから削除とマークされています。  
- : デタッチされたエンティティがないコンテキストによって追跡されて、  

SaveChanges はさまざまな状態のエンティティのさまざまな処理を行います。  

- SaveChanges では、変更されていないエンティティは変更されません。 更新プログラムは、エンティティ、Unchanged 状態のデータベースには送信されません。  
- 追加エンティティは、データベースに挿入され、しになる変更されずに SaveChanges を返します。  
- 変更されたエンティティは、データベースが更新され、しになる変更されずに SaveChanges を返します。  
- 削除されたエンティティは、データベースから削除して、コンテキストからデタッチされますし。  

次の例では、エンティティまたはエンティティ グラフの状態を変更できる方法を示します。  

## <a name="adding-a-new-entity-to-the-context"></a>新しいエンティティをコンテキストに追加します。  

新しいエンティティは、DbSet の Add メソッドを呼び出すことによって、コンテキストに追加できます。
これは、エンティティを Added 状態で、つまりことにはデータベースに挿入される、次回の SaveChanges が呼び出されるとします。
例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

コンテキストに新しいエンティティを追加する別の方法では、Added にその状態を変更します。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

最後に、別のエンティティは既に追跡されている最大フックして、コンテキストに新しいエンティティを追加できます。
別のエンティティのコレクション ナビゲーション プロパティに新しいエンティティを追加するか、新しいエンティティをポイントする別のエンティティの参照ナビゲーション プロパティを設定することが考えられます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    var blog = context.Blogs.Find(2);
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

追跡し、これらの新しいエンティティがまだ追加されているエンティティがないその他のエンティティへの参照を持つ場合、これらの例のすべてのことは、コンテキストにも追加され、次回 SaveChanges が呼び出されるとに、データベースに挿入されます。  

## <a name="attaching-an-existing-entity-to-the-context"></a>既存のエンティティをコンテキストにアタッチ  

ある場合は、データベースが、これは現在によって追跡されていないコンテキスト DbSet の Attach メソッドを使用してエンティティを追跡するためにコンテキストを指示に既にわかっているエンティティが存在します。 エンティティは、コンテキストで変更されていない状態になります。 例えば:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

変更されないことをデータベースに接続されているエンティティの他の操作を実行せずに SaveChanges が呼び出された場合に注意してください。 これは、エンティティが Unchanged 状態であるためにです。  

既存のエンティティをコンテキストにアタッチする別の方法では、Unchanged にその状態を変更します。 例えば:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

これらの例の両方にアタッチされるエンティティがまだ追跡されていない他のエンティティへの参照を持つ場合、これらの新しいエンティティもに接続されている Unchanged 状態でコンテキストに注意してください。  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>コンテキストに変更されたエンティティが、既存のアタッチ  

ある場合は、どの変更可能性がありますが行われたエンティティをアタッチし、その状態を Modified に設定するコンテキストを指示するが、データベース内に既にわかっているエンティティが存在します。
例えば:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

Modified 状態を変更して、エンティティのすべてのプロパティは変更済みとしてマークはすべてのプロパティ値は、SaveChanges が呼び出されたときに、データベースに送信されます。  

アタッチされるエンティティにまだ追跡されていない他のエンティティへの参照がある場合は、し、これらの新しいエンティティが接続されている Unchanged 状態でコンテキストに注意してください: ない自動的に行うことが変更されました。
変更済みとしてマークする必要がある複数のエンティティがある場合の各エンティティの状態を個別に設定する必要があります。  

## <a name="changing-the-state-of-a-tracked-entity"></a>追跡対象エンティティの状態の変更  

エントリに、State プロパティを設定して追跡されるエンティティの状態を変更することができます。 例えば:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

既に追跡されているエンティティの追加またはアタッチを呼び出すこともでき、エンティティの状態を変更するに注意してください。 たとえば、Added 状態であるエンティティの Attach の呼び出しはその状態を変更 Unchanged。  

## <a name="insert-or-update-pattern"></a>挿入または更新パターン  

一部のアプリケーションの一般的なパターンは、新しい (その結果、データベースの挿入) としてエンティティを追加するか、またはとして既存のエンティティをアタッチし、(その結果、データベースの更新) を変更済みとしてマークする主キーの値によって決まります。
たとえば、データベースで生成された整数の主キーを使用する場合は、新規として 0 個のキーを持つエンティティと既存として 0 以外のキーを持つエンティティを処理する一般的なは。
このパターンは、主キーの値のチェックに基づくエンティティの状態を設定して実現できます。 例えば:  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

Modified 状態を変更すると、エンティティのすべてのプロパティは変更済みとしてマークされ、すべてのプロパティ値は、SaveChanges が呼び出されたときに、データベースに送信されますに注意してください。  
