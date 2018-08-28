---
title: ローカル データ - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
ms.openlocfilehash: dac1a1de20398501c706b118443743d47970df17
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994275"
---
# <a name="local-data"></a>ローカル データ
DbSet に対して直接、LINQ クエリを実行しているは常にクエリを送信、データベースが現在のインメモリ DbSet.Local プロパティを使用しているデータにアクセスすることができます。 EF を追跡する追加情報は、DbContext.Entry および DbContext.ChangeTracker.Entries メソッドを使用してエンティティについてアクセスすることもできます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="using-local-to-look-at-local-data"></a>ローカルを使用してローカル データを確認するには  

DbSet のローカルのプロパティは、現在のコンテキストによって追跡されているし、削除済みとしてマークされていない、一連のエンティティにシンプルなアクセスを提供します。 データベースに送信されるクエリが、ローカル プロパティにアクセスします。 クエリの実行後、通常、使用はことを意味これです。 クエリの実行コンテキストは、結果を追跡するために、Load 拡張メソッドを使用できます。 例えば:  

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

2 つのブログ - 'ADO.NET Blog' の 1 の BlogId で' The Visual Studio Blog' の 2 の BlogId でのデータベースがある場合は、次の出力を予定でした。  

```  
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

これは、3 つの点を示しています。  

- 新しいブログ 'マイ ブログの新しい' は、データベースに保存されていない場合でも、ローカル コレクションに含まれます。 このブログは、データベースの表示、エンティティの実際のキーがまだ生成するために、ゼロの主キーを持ちます。  
- コンテキストがまだ追跡されている場合でも、ADO.NET ブログはローカル コレクションに含まれていません。 これは、ため、削除済みとしてマークする DbSet から削除したためです。  
- DbSet を使用してクエリを実行する (ADO.NET のブログ) を削除対象としてマークするブログが結果に含めるし、データベースに保存されていない新しいブログ (マイ新しいブログ) は、結果に含まれていません。 DbSet がデータベースに対してクエリを実行して、常に返される結果が、データベースの新機能を反映するためです。  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>ローカルを使用して追加し、コンテキストからエンティティを削除するには  

DbSet にローカル プロパティを返します、 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx)コンテキストの内容との同期に保持されるようにフックするイベントです。 これは、エンティティの追加またはローカル コレクションや、DbSet のいずれかから削除されることを意味します。 また、新しいエンティティをコンテキストに読み込むクエリはこれらのエンティティで更新されているローカル コレクションになります。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework").Load();  

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

' Entity framework' と 'asp.net' 出力のタグ付けされたいくつか投稿ことが前提と次のように見えます。  

```  
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

これは、3 つの点を示しています。  

- 'New in EF は' 新しい投稿追加されたローカル コレクションが Added 状態でコンテキストによって追跡になります。 そのため挿入のデータベースに SaveChanges が呼び出されるとします。  
- ローカル コレクション (EF 初心者向けガイド) から削除された投稿は、コンテキストで削除済みとしてマークされているようになりました。 削除されます、データベースから SaveChanges が呼び出されるとします。  
- 2 番目のクエリを使用して、コンテキストに読み込まれるその他の投稿 (ASP.NET の初心者向けガイド) は、ローカル コレクションに自動的に追加されます。  

ローカルに関する注意事項を 1 つの最後には、ObservableCollection パフォーマンスは多数のエンティティに適していないためです。 そのためのコンテキストで何千ものエンティティを扱う場合ある可能性がありますいないローカルを使用することをお勧めします。  

## <a name="using-local-for-wpf-data-binding"></a>WPF データ バインディングのローカルを使用します。  

DbSet にローカル プロパティは、ObservableCollection のインスタンスであるため、WPF アプリケーションでのデータ バインディングを直接使用できます。 つまり、自動的には、前のセクションで説明されている同期コンテキストの内容を維持し、コンテキストの内容は自動的に同期して最新します。 データベース クエリが、ローカルにバインドするものにするデータを使用してローカル コレクションを事前に設定する必要に注意してください。  

これは適切な場所の完全な WPF データ バインド サンプルではありませんが、主な要素は。  

- バインディング ソースのセットアップ  
- セットのローカル プロパティにバインドします。  
- データベースにクエリを使用してローカルに設定します。  

## <a name="wpf-binding-to-navigation-properties"></a>ナビゲーション プロパティを WPF のバインド  

実行している場合マスター/詳細データをバインドすることがあります、エンティティのいずれかのナビゲーション プロパティに詳細ビューをバインドするとします。 この作業を行う簡単な方法では、ナビゲーション プロパティの ObservableCollection を使用します。 例えば:  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>ローカルを使用して、SaveChanges 内のエンティティをクリーンアップするには  

エンティティのナビゲーション プロパティから削除ほとんどの場合、コンテキストで削除済みとして自動的にマークされません。 たとえば、Blog.Posts コレクションから投稿する Post オブジェクトを削除する場合は自動的に削除されません SaveChanges が呼び出されるとします。 削除する必要がある場合は、これらの未解決のエンティティを見つけて、SaveChanges を呼び出す前に、またはオーバーライドの SaveChanges の一部として削除済みとしてマークする必要があります。 例えば:  

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

上記のコードでは、ローカルのコレクションを使用して、すべての投稿とをブログの参照を持たない場合削除済みとしてマークを見つけます。 それ以外の場合、削除によってコレクションが変更されるため、ToList 呼び出しが必要ですが列挙されているときに呼び出します。 他のほとんどの状況では、ToList をまずを使用せずローカル プロパティを直接照会できます。  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>ローカルおよび ToBindingList の Windows フォーム データ バインディングの使用  

Windows フォームは、ObservableCollection を直接使用する完全な忠実度のデータ バインディングをサポートしていません。 ただし、前のセクションで説明されているすべての利点を得るため、データ バインディングの DbSet ローカル プロパティを使用できます。 これを作成する ToBindingList 拡張メソッドによって実現されますが、 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)実装、ローカルの ObservableCollection によってバックアップされています。  

これは適切な場所の完全な Windows フォーム データ バインド サンプルではありませんが、主な要素は。  

- オブジェクトのバインディング ソースをセットアップします。  
- Local.ToBindingList() を使用して、セットのローカル プロパティにバインドします。  
- データベースにクエリを使用してローカルを設定します。  

## <a name="getting-detailed-information-about-tracked-entities"></a>追跡対象のエンティティに関する詳細な情報の取得  

このシリーズの例の多くは、エンティティの対象の DbEntityEntry インスタンスを返すエントリ メソッドを使用します。 このエントリのオブジェクトは、エンティティなど、現在の状態に関する情報を収集すると、関連エンティティの明示的な読み込みなどのエンティティに対して操作を実行するための出発点として機能します。  

エントリ メソッドでは、コンテキストによって追跡されている多くまたはすべてのエンティティの対象の DbEntityEntry オブジェクトを返します。 これにより、情報を収集または 1 つのエントリだけではなく、多数のエンティティに対して操作を実行することができます。 例えば:  

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

例では、作成者、閲覧者のクラスが導入されています - IPerson インターフェイスを実装してこれらのクラスの両方がわかります。  

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

データベースに、次のデータがあると仮定します。

BlogId ブログ = 1、名前 = ' ADO.NET ブログ '  
BlogId ブログ = 2、名前 = 'Visual Studio ブログ'  
BlogId ブログ = 3 および名 = '.NET Framework ブログ'  
著者 Id で作成者 = 1、名前 = ' Joe Bloggs'  
使用した ReaderId リーダー = 1、名前 = ' John Doe'  

コードの実行からの出力は次のようになります。  

```  
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

これらの例は、いくつかの点を示しています。  

- エントリ メソッドでは、削除済みを含む、すべての状態でエンティティのエントリを返します。 これと比較を除くローカル エンティティを削除します。  
- エントリの非ジェネリック メソッドを使用する場合は、すべてのエンティティ型のエントリが返されます。 エントリのジェネリック メソッドを使用する場合エントリはジェネリック型のインスタンスであるエンティティに対してのみ返されます。 これは、上のすべてのブログのエントリを取得に使用されました。 でも、IPerson を実装するすべてのエンティティのエントリの取得に使用されていました。 これは、実際のエンティティ型をジェネリック型がないことを示します。  
- オブジェクトに LINQ を使用して、返される結果をフィルター処理できます。 これは、上記変更される場合に限り、任意の型のエンティティを検索に使用されました。  

対象の DbEntityEntry インスタンスが常に null 以外のエンティティを含めることことに注意してください。 リレーションシップ エントリとスタブ エントリは表されませんとして対象の DbEntityEntry インスタンスのため、これらのフィルター処理する必要はありません。
