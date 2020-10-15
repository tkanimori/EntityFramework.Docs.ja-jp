---
title: エンティティの状態の操作-EF6
description: Entity Framework 6 でのエンティティ状態の操作
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 5373f7157882062e8a73b2bd414c6a8b9accdba4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064459"
---
# <a name="working-with-entity-states"></a>エンティティの状態の操作
このトピックでは、コンテキストにエンティティを追加してアタッチする方法と、それらを SaveChanges 中に Entity Framework 処理する方法について説明します。
Entity Framework は、コンテキストへの接続中にエンティティの状態を追跡しますが、切断されたシナリオや N 層のシナリオでは、エンティティの状態を EF に知らせることができます。
このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="entity-states-and-savechanges"></a>エンティティの状態と SaveChanges

エンティティは、EntityState 列挙で定義されている5つの状態のいずれかになります。 これらの状態を次に示します。  

- 追加: エンティティはコンテキストによって追跡されていますが、データベースにまだ存在していません  
- Unchanged: エンティティがコンテキストによって追跡され、データベースに存在し、そのプロパティ値がデータベース内の値から変更されていません。  
- Modified: エンティティがコンテキストによって追跡され、データベースに存在し、そのプロパティ値の一部またはすべてが変更されています。  
- Deleted: エンティティがコンテキストによって追跡されていて、データベースに存在するが、次回 SaveChanges が呼び出されたときにデータベースから削除対象としてマークされている  
- デタッチ済み: エンティティがコンテキストによって追跡されていません  

SaveChanges は、さまざまな状態のエンティティに対してさまざまな処理を行います。  

- 変更されていないエンティティは SaveChanges によって操作されません。 更新プログラムは、変更されていない状態のエンティティのデータベースには送信されません。  
- 追加されたエンティティはデータベースに挿入され、SaveChanges が返されるときには変更されません。  
- 変更されたエンティティはデータベースで更新され、SaveChanges が戻るときに変更されないようになります。  
- 削除されたエンティティはデータベースから削除され、その後コンテキストからデタッチされます。  

次の例は、エンティティまたはエンティティグラフの状態を変更できる方法を示しています。  

## <a name="adding-a-new-entity-to-the-context"></a>コンテキストへの新しいエンティティの追加  

DbSet で Add メソッドを呼び出すことによって、新しいエンティティをコンテキストに追加できます。
これにより、エンティティは追加された状態になります。つまり、次に SaveChanges が呼び出されたときにデータベースに挿入されます。
次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

コンテキストに新しいエンティティを追加するもう1つの方法は、その状態を "追加" に変更することです。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

最後に、既に追跡されている別のエンティティにフックすることによって、新しいエンティティをコンテキストに追加できます。
これは、別のエンティティのコレクションナビゲーションプロパティに新しいエンティティを追加するか、新しいエンティティを指す別のエンティティの参照ナビゲーションプロパティを設定することによって行うことができます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

これらのすべての例では、追加するエンティティがまだ追跡されていない他のエンティティを参照している場合、これらの新しいエンティティもコンテキストに追加され、次に SaveChanges が呼び出されたときにデータベースに挿入されます。  

## <a name="attaching-an-existing-entity-to-the-context"></a>既存のエンティティをコンテキストにアタッチする  

データベースに既に存在しているが、現在コンテキストによって追跡されていないエンティティがある場合は、DbSet で Attach メソッドを使用してエンティティを追跡するようにコンテキストに指示できます。 エンティティは、コンテキスト内の変更されていない状態になります。 次に例を示します。  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

アタッチされたエンティティに対して他の操作を行わずに SaveChanges を呼び出すと、データベースは変更されないことに注意してください。 これは、エンティティが変更されていない状態であるためです。  

既存のエンティティをコンテキストにアタッチするもう1つの方法は、その状態を変更なしに変更することです。 次に例を示します。  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

これらの両方の例で、アタッチされているエンティティがまだ追跡されていない他のエンティティを参照している場合は、これらの新しいエンティティも変更されていない状態でコンテキストにアタッチされます。  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>既存の変更されたエンティティをコンテキストにアタッチする  

データベースに既に存在し、変更が加えられていることがわかっているエンティティがある場合は、エンティティをアタッチするようにコンテキストに指示し、その状態を Modified に設定できます。
次に例を示します。  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

状態を "変更済み" に変更すると、エンティティのすべてのプロパティが変更済みとしてマークされ、SaveChanges が呼び出されたときにすべてのプロパティ値がデータベースに送信されます。  

アタッチされているエンティティにまだ追跡されていない他のエンティティへの参照が含まれている場合、これらの新しいエンティティは、変更されていない状態でコンテキストにアタッチされるため、自動的に変更されることはありません。
複数のエンティティを変更済みとしてマークする必要がある場合は、これらの各エンティティの状態を個別に設定する必要があります。  

## <a name="changing-the-state-of-a-tracked-entity"></a>追跡対象エンティティの状態の変更  

エントリの状態プロパティを設定することによって、既に追跡されているエンティティの状態を変更できます。 次に例を示します。  

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

既に追跡されているエンティティに対して追加またはアタッチを呼び出すと、エンティティの状態を変更することもできます。 たとえば、現在追加されているエンティティの Attach を呼び出すと、その状態が Unchanged に変わります。  

## <a name="insert-or-update-pattern"></a>パターンの挿入または更新  

アプリケーションによっては、主キーの値に応じて、エンティティを新規として追加するか (データベースの挿入を結果として)、エンティティを既存のものとしてアタッチし、変更済みとしてマークする (データベースの更新を作成する) ことがあります。
たとえば、データベースによって生成された整数の主キーを使用する場合は、エンティティを new として、0以外のキーを持つエンティティを既存のエンティティとして扱うのが一般的です。
このパターンは、主キーの値のチェックに基づいてエンティティの状態を設定することによって実現できます。 次に例を示します。  

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

状態を "変更済み" に変更すると、エンティティのすべてのプロパティが変更済みとしてマークされ、SaveChanges が呼び出されたときにすべてのプロパティ値がデータベースに送信されることに注意してください。  
