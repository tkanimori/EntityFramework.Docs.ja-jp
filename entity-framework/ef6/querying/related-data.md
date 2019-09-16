---
title: 関連エンティティを読み込んでいます-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: c359d8d32a88049213fd5e98e99fe49d7e3121a3
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005485"
---
# <a name="loading-related-entities"></a>関連エンティティを読み込んでいます

Entity Framework は、関連するデータの一括読み込み、遅延読み込み、および明示的な読み込みを読み込む3つの方法をサポートしています。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。

## <a name="eagerly-loading"></a>集中的の読み込み

一括読み込みは、ある種類のエンティティに対するクエリでも、クエリの一部として関連エンティティを読み込むプロセスです。 一括読み込みは、Include メソッドを使用して実現されます。 たとえば、次のクエリでは、ブログと各ブログに関連するすべての投稿が読み込まれます。

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include は、system.string 名前空間の拡張メソッドであるため、その名前空間を使用していることを確認してください。

### <a name="eagerly-loading-multiple-levels"></a>集中的複数レベルの読み込み

また、関連エンティティの複数のレベルを集中的に読み込むこともできます。 次のクエリは、コレクションと参照の両方のナビゲーションプロパティでこの操作を行う方法の例を示しています。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> 現在、どの関連エンティティが読み込まれているかをフィルター処理することはできません。 Include は常に、関連するすべてのエンティティを取り込みます。  

## <a name="lazy-loading"></a>遅延読み込み

遅延読み込みは、エンティティまたはエンティティを参照するプロパティがアクセスされるときに、エンティティまたはエンティティのコレクションがデータベースから自動的に読み込まれるようにするプロセスです。 POCO エンティティ型を使用する場合、遅延読み込みは、派生プロキシ型のインスタンスを作成してから、仮想プロパティをオーバーライドして読み込みフックを追加することで実現されます。 たとえば、次に定義されているブログエンティティクラスを使用する場合、投稿ナビゲーションプロパティに初めてアクセスするときに関連する投稿が読み込まれます。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>シリアル化の遅延読み込みをオフにする

遅延読み込みとシリアル化にはあまり適していません。また、遅延読み込みが有効になっているので、データベース全体のクエリを終了することもできます。 ほとんどのシリアライザーは、型のインスタンスの各プロパティにアクセスすることによって機能します。 プロパティアクセスは遅延読み込みをトリガーするため、より多くのエンティティがシリアル化されます。 これらのエンティティにはプロパティがアクセスされ、さらに多くのエンティティが読み込まれます。 エンティティをシリアル化する前に、遅延読み込みをオフにすることをお勧めします。 次のセクションでは、その方法について説明します。

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>特定のナビゲーションプロパティの遅延読み込みをオフにする

Post コレクションの遅延読み込みを無効にするには、Post プロパティを非仮想にします。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

Post コレクションの読み込みは、一括読み込みを使用して行うことができます (上記の「*集中的の読み込み*」を参照してください)。または Load メソッドを使用します (以下の「*明示的な読み込み*」を参照)。

### <a name="turn-off-lazy-loading-for-all-entities"></a>すべてのエンティティの遅延読み込みをオフにする

遅延読み込みは、構成プロパティにフラグを設定することによって、コンテキスト内のすべてのエンティティに対して無効にすることができます。 例えば:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

関連エンティティの読み込みは、一括読み込みを使用して実行できます (上記の「*集中的の読み込み*」を参照してください)。または Load メソッドを使用します (以下の「*明示的な読み込み*」を参照)。

## <a name="explicitly-loading"></a>明示的な読み込み

遅延読み込みが無効になっていても、関連エンティティを遅延読み込みすることはできますが、明示的な呼び出しを使用して実行する必要があります。 これを行うには、関連エンティティのエントリに対して Load メソッドを使用します。 例えば:

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> 参照メソッドは、エンティティに別の1つのエンティティへのナビゲーションプロパティがある場合に使用する必要があります。 一方、エンティティに他のエンティティのコレクションへのナビゲーションプロパティがある場合は、コレクションメソッドを使用する必要があります。

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>関連エンティティを明示的に読み込むときにフィルターを適用する

クエリメソッドを使用すると、関連エンティティを読み込むときに使用 Entity Framework 基になるクエリにアクセスできます。 その後、LINQ を使用してクエリにフィルターを適用してから、そのクエリを実行する前に、ToList、Load などの LINQ 拡張メソッドを呼び出すことができます。クエリメソッドは参照とコレクションの両方のナビゲーションプロパティと共に使用できますが、コレクションの一部だけを読み込むために使用できるコレクションに最も役立ちます。 例えば:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

クエリメソッドを使用する場合は、通常、ナビゲーションプロパティの遅延読み込みを無効にすることをお勧めします。 これは、フィルター選択されたクエリの実行前または実行後に、遅延読み込みメカニズムによってコレクション全体が自動的に読み込まれるためです。

> [!NOTE]
> リレーションシップは、ラムダ式の代わりに文字列として指定できますが、文字列が使用されている場合、返される IQueryable はジェネリックではないため、通常はキャストメソッドを使用する必要があります。

## <a name="using-query-to-count-related-entities-without-loading-them"></a>クエリを使用して関連エンティティを読み込んでカウントする

場合によっては、データベース内の別のエンティティに関連付けられているエンティティの数を把握することが役に立つことがあります。 LINQ Count メソッドを使用したクエリメソッドを使用してこれを行うことができます。 例えば:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
