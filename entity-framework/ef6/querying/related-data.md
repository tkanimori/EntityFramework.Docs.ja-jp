---
title: 関連エンティティの EF6 の読み込み
author: divega
ms.date: 2016-10-23
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: 091493f60c732573ca63adb8c65bc28606453d34
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995926"
---
# <a name="loading-related-entities"></a>関連エンティティを読み込む
Entity Framework には、関連データの一括読み込み、遅延読み込みと明示的な読み込みを読み込む 3 つの方法がサポートしています。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="eagerly-loading"></a>集中読み込み  

一括読み込みは、このエンティティの 1 つの種類のクエリ関連エンティティも読み込むクエリの一部としてプロセスです。 一括読み込みは、Include メソッドを使用して実現されます。 たとえば、下記のクエリは、ブログや各ブログに関連するすべての投稿を読み込みます。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                          .Include(b => b.Posts)
                          .ToList();

    // Load one blogs and its related posts
    var blog1 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include(b => b.Posts)
                        .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                          .Include("Posts")
                          .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include("Posts")
                        .FirstOrDefault();
}
```  

System.Data.Entity 名前空間での拡張メソッドは、含めるようにしてその名前空間を使用しています。  

### <a name="eagerly-loading-multiple-levels"></a>複数のレベルを集中的読み込み  

関連エンティティの複数のレベルを集中的に読み込むこともできます。 次のクエリでは、コレクションと参照ナビゲーション プロパティの両方にこれを行う方法の例を紹介します。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                       .Include(b => b.Posts.Select(p => p.Comments))
                       .ToList();

    // Load all users their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                       .Include("Posts.Comments")
                       .ToList();

    // Load all users their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

現在、どの関連エンティティが読み込まれるフィルターすることはできませんに注意してください。 Include は常にすべての関連エンティティを取り込みます。  

## <a name="lazy-loading"></a>遅延読み込み  

遅延読み込みは、というエンティティまたはエンティティのコレクションが自動的に初めて読み込まれる、データベースからエンティティ/エンティティを参照するプロパティにアクセスするプロセスです。 POCO エンティティ型を使用する場合、遅延読み込みは、プロキシの派生型のインスタンスを作成して、読み込み用のフックを追加する仮想プロパティをオーバーライドし、によって実現されます。 たとえば、以下に定義されたブログ エンティティ クラスを使用する場合、投稿のナビゲーション プロパティにアクセス最初時に、関連する投稿が読み込まれます。  

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

### <a name="turn-lazy-loading-off-for-serialization"></a>遅延のシリアル化の読み込みをオフにします。  

遅延読み込みとシリアル化しないし、同時に、注意が必要ない場合は、遅延読み込みが有効になっているからといって、データベース全体のクエリを終了できます。 ほとんどのシリアライザーは、型のインスタンス上の各プロパティにアクセスする機能します。 プロパティへのアクセスは、以上のエンティティ シリアル化するため、遅延読み込みをトリガーします。 これらのエンティティのプロパティにアクセス、およびさらに多くのエンティティが読み込まれます。 エンティティをシリアル化する前に、無効の読み込み遅延を有効にすることをお勧めします。 次のセクションでは、これを行う方法を示します。  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>特定のナビゲーション プロパティの遅延読み込みをオフにします。  

投稿のプロパティを仮想でないことで、投稿コレクションの遅延読み込みをオフにすることができます。  

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

読み込みの投稿のコレクションも実現できます一括読み込みを使用して (を参照してください*集中読み込み*上) または Load メソッド (を参照してください*の明示的な読み込み*以下)。  

### <a name="turn-off-lazy-loading-for-all-entities"></a>すべてのエンティティの遅延読み込みをオフにします。  

遅延読み込みは、そのコンテキスト内のすべてのエンティティの構成プロパティにフラグを設定して無効にできます。 例えば:  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

関連エンティティの読み込みは一括読み込み実現することができます (を参照してください*集中読み込み*上) または Load メソッド (を参照してください*の明示的な読み込み*以下)。  

## <a name="explicitly-loading"></a>明示的な読み込み  

遅延読み込みを無効にしても、関連エンティティを遅延読み込みすることも可能ですが、明示的な呼び出しを実行してください。 これを行うには、関連エンティティのエントリの Load メソッドを使用します。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

エンティティに別の単一のエンティティにナビゲーション プロパティがあるときに参照メソッドを使用する必要がありますに注意してください。 その一方で、エンティティの他のエンティティのコレクションにナビゲーション プロパティがある場合は、収集方法を使用してください。  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>関連エンティティを明示的に読み込むときに、フィルターを適用します。  

クエリ メソッドは、関連エンティティの読み込み時に Entity Framework を使用する基になるクエリへのアクセスを提供します。 LINQ を使用して、ToList や負荷などの LINQ 拡張メソッドの呼び出しを使用して実行する前に、クエリにフィルターを適用することができます。クエリ メソッドは、ナビゲーション プロパティの参照とコレクションの両方で使用できますがコレクションに最も役立つコレクションの一部のみに使用できます。 例えば:  

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

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
        .Collection("Posts")
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  

クエリ メソッドを使用する場合は、ナビゲーション プロパティの遅延読み込みをオフに、通常はお勧めします。 これは、ため、それ以外の場合コレクション全体がによって読み込まれる自動的に遅延読み込みメカニズム前に、またはフィルター選択されたクエリが実行された後です。  

リレーションシップは、ラムダ式ではなく文字列として指定できます、返された対象の IQueryable ではありません汎用文字列を使用すると、そのため、有益なもののように、前に、キャスト メソッドは通常必要に注意してください。  

## <a name="using-query-to-count-related-entities-without-loading-them"></a>それらを読み込むことがなく、関連エンティティをカウントするクエリを使用します。  

場合によっては実際にこれらすべてのエンティティの読み込みのコストをかけずに、データベース内の別のエンティティに関連エンティティの数を把握するのに役立ちます。 これを行うには、LINQ カウント メソッドを使用して、クエリ メソッドを使用できます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                          .Collection(b => b.Posts)
                          .Query()
                          .Count();
}
```  
