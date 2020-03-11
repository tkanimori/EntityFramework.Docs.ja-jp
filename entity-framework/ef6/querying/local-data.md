---
title: ローカルデータ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
ms.openlocfilehash: efd646348d8a18bbeed2d0a0e708d4d36eb26eac
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414483"
---
# <a name="local-data"></a>ローカル データ
DbSet に対して LINQ クエリを直接実行すると、常にクエリがデータベースに送信されますが、現在、DbSet. Local プロパティを使用してメモリ内にあるデータにアクセスできます。 また、DbContext. Entry メソッドと DbContext. ChangeTracker メソッドを使用して、エンティティに関する追加情報 EF が追跡している情報にアクセスすることもできます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="using-local-to-look-at-local-data"></a>ローカルデータの参照にローカルを使用する  

DbSet のローカルプロパティは、現在コンテキストによって追跡されていて、削除済みとしてマークされていないセットのエンティティへの単純なアクセスを提供します。 ローカルプロパティにアクセスしても、クエリがデータベースに送信されることはありません。 これは、通常、クエリが既に実行された後に使用されることを意味します。 読み込み拡張メソッドを使用してクエリを実行し、コンテキストが結果を追跡できるようにすることができます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

ブログ Id が1で、ブログ id が2である ' ADO.NET Blog ' というデータベースに2つのブログがある場合、次の出力が予想される可能性があります。  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

これは、3つの点を示しています。  

- 新しいブログ「My New Blog」は、データベースにまだ保存されていない場合でも、ローカルコレクションに含まれています。 このブログでは、エンティティの実際のキーがデータベースによって生成されていないため、主キーが0になっています。  
- コンテキストによって追跡されているにもかかわらず、' ADO.NET Blog ' はローカルコレクションに含まれていません。 これは、DbSet から削除されたため、削除済みとしてマークされているためです。  
- DbSet を使用してクエリを実行すると、削除のマークが付いているブログ (ADO.NET Blog) が結果に含まれ、データベースに保存されていない新しいブログ (My New Blog) は結果に含まれません。 これは、DbSet がデータベースに対してクエリを実行し、返される結果には常にデータベースの内容が反映されるためです。  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>ローカルを使用してコンテキストのエンティティを追加および削除する  

DbSet の Local プロパティは、コンテキストのコンテンツと同期したままになるように、イベントがフックされた[system.collections.objectmodel.observablecollection](https://msdn.microsoft.com/library/ms668604.aspx)を返します。 これは、ローカルコレクションまたは DbSet からエンティティを追加または削除できることを意味します。 また、新しいエンティティをコンテキストに取り込むクエリによって、そのエンティティでローカルコレクションが更新されることも意味します。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

' Entity-framework ' と ' asp.net ' にタグ付けされたいくつかの投稿があると仮定した場合、出力は次のようになります。  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

これは、3つの点を示しています。  

- ローカルコレクションに追加された新しい post ' EF の新機能 ' は、追加された状態のコンテキストによって追跡されます。 そのため、SaveChanges が呼び出されると、データベースに挿入されます。  
- ローカルコレクションから削除された投稿 (EF 初心者ガイド) は、コンテキストで削除済みとしてマークされるようになりました。 そのため、SaveChanges が呼び出されると、データベースから削除されます。  
- 2番目のクエリを使用してコンテキストに読み込まれた追加の post (ASP.NET 初心者ガイド) は、自動的にローカルコレクションに追加されます。  

ローカルに関する最後の注意点としては、System.collections.objectmodel.observablecollection のパフォーマンスは、大量のエンティティには適していません。 そのため、コンテキスト内で数千のエンティティを扱う場合は、ローカルを使用することをお勧めしません。  

## <a name="using-local-for-wpf-data-binding"></a>WPF データバインディングにローカルを使用する  

DbSet のローカルプロパティは、System.collections.objectmodel.observablecollection のインスタンスであるため、WPF アプリケーションのデータバインディングに直接使用できます。 前のセクションで説明したように、これはコンテキストのコンテンツと自動的に同期されることを意味し、コンテキストの内容は自動的に同期されます。 ローカルコレクションにバインドするデータを事前に設定する必要があることに注意してください。これは、Local がデータベースクエリを発生させないためです。  

これは、完全な WPF データバインディングサンプルでは適切な場所ではありませんが、主な要素は次のとおりです。  

- バインディングソースの設定  
- Set のローカルプロパティにバインドする  
- データベースにクエリを使用してローカルにデータを設定します。  

## <a name="wpf-binding-to-navigation-properties"></a>WPF のナビゲーションプロパティへのバインド  

マスター/詳細データのバインドを行う場合は、いずれかのエンティティのナビゲーションプロパティに詳細ビューをバインドすることができます。 この作業を行う簡単な方法は、ナビゲーションプロパティに System.collections.objectmodel.observablecollection を使用することです。 次に例を示します。  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>ローカルを使用して SaveChanges でエンティティをクリーンアップする  

ほとんどの場合、ナビゲーションプロパティから削除されたエンティティは、コンテキストで削除済みとして自動的にマークされません。 たとえば、Post オブジェクトを Blog コレクションから削除した場合、SaveChanges が呼び出されても、その投稿は自動的には削除されません。 削除する必要がある場合は、SaveChanges を呼び出す前、またはオーバーライドされた SaveChanges の一部として、これらのぶら下がりエンティティを検索し、削除済みとしてマークする必要があります。 次に例を示します。  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

上記のコードでは、ローカルコレクションを使用してすべての投稿を検索し、ブログ参照が削除済みとして含まれていないをマークします。 それ以外の場合は、列挙中に削除呼び出しによってコレクションが変更されるため、ToList 呼び出しが必要です。 その他のほとんどの状況では、ToList first を使用せずに、ローカルプロパティに対して直接クエリを実行できます。  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>Windows フォームデータバインディングにローカルと ToBindingList を使用する  

Windows フォームは、System.collections.objectmodel.observablecollection を直接使用した忠実度の高いデータバインディングをサポートしていません。 ただし、前のセクションで説明したすべての利点を取得するには、データバインディングに対して DbSet ローカルプロパティを使用することもできます。 これは、ローカルの System.collections.objectmodel.observablecollection によってサポートされる[IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)実装を作成する ToBindingList 拡張メソッドを使用して実現されます。  

これは、完全 Windows フォームデータバインディングサンプルには適していませんが、主な要素は次のとおりです。  

- オブジェクトのバインドソースの設定  
- ToBindingList () を使用して、セットのローカルプロパティにバインドします。  
- データベースにクエリを使用してローカルにデータを設定する  

## <a name="getting-detailed-information-about-tracked-entities"></a>追跡対象エンティティに関する詳細情報の取得  

このシリーズの多くの例では、Entry メソッドを使用して、エンティティの DbEntityEntry インスタンスを返しています。 このエントリオブジェクトは、現在の状態などのエンティティに関する情報を収集するための開始点として機能します。また、関連エンティティの明示的な読み込みなど、エンティティに対する操作の実行にも使用されます。  

Entries メソッドは、コンテキストによって追跡されている多数またはすべてのエンティティに対して DbEntityEntry オブジェクトを返します。 これにより、情報を収集したり、1つのエントリだけではなく、多くのエンティティに対して操作を実行したりすることができます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

例に著者とリーダークラスが導入されていることがわかります。これらのクラスはどちらも IPerson インターフェイスを実装しています。  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

データベースに次のデータが含まれているとします。

Blog Id = 1、Name = ' ADO.NET Blog ' のブログ  
ブログ Id = 2、名前 = ' Visual Studio ブログ '  
ブログ Id = 3、名前 = ' .NET Framework ブログ '  
AuthorId = 1、Name = ' Joe ブログ Gs ' を使用した作成者  
ReaderId = 1、Name = ' John Doe ' のリーダー  

コードを実行した場合の出力は次のようになります。  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

次の例は、いくつかの点を示しています。  

- Entries メソッドは、Deleted を含むすべての状態のエンティティのエントリを返します。 これをローカルと比較して、削除されたエンティティを除外します。  
- 非ジェネリックエントリメソッドを使用すると、すべてのエンティティ型のエントリが返されます。 汎用エントリメソッドが使用されている場合、エントリは、ジェネリック型のインスタンスであるエンティティに対してのみ返されます。 これは、すべてのブログのエントリを取得するために上記で使用されていました。 また、IPerson を実装するすべてのエンティティのエントリを取得するためにも使用されていました。 これは、ジェネリック型が実際のエンティティ型である必要がないことを示しています。  
- LINQ to Objects は、返される結果をフィルター処理するために使用できます。 これは、変更されている限り、任意の型のエンティティを検索するために上記で使用されていました。  

DbEntityEntry インスタンスには、常に null 以外のエンティティが含まれていることに注意してください。 リレーションシップエントリとスタブエントリは DbEntityEntry インスタンスとしては表されないため、これらをフィルター処理する必要はありません。
