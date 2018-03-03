---
title: "EF コアのコンス トラクターを持つエンティティ型"
author: ajcvickers
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
ms.technology: entity-framework-core
uid: core/modeling/constructors
ms.openlocfilehash: 38ab0c1c3cd8c490875abf30b8478c99bc58630f
ms.sourcegitcommit: 60b831318c4f5ec99061e8af6a7c9e7c03b3469c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="entity-types-with-constructors"></a>コンス トラクターを持つエンティティ型

> [!NOTE]  
> この機能は、EF コア 2.1 の新機能です。

EF コア 2.1 から始めて、場合は、パラメーターを持つコンス トラクターを定義し、エンティティのインスタンスを作成するときに、このコンス トラクターを呼び出す EF コアを可能なようになりました。 コンス トラクターのパラメーターは、マップされているプロパティにバインドできます。 または動作を容易にするサービスのさまざまな種類には遅延読み込みと同様にします。

> [!NOTE]  
> EF コア 2.1 では、時点では、すべてのコンス トラクターのバインディングは、慣例です。 使用する特定のコンス トラクターの構成は、今後のリリースされる予定です。

## <a name="binding-to-mapped-properties"></a>マップされたプロパティへのバインド

一般的なブログの投稿/モデルを考慮してください。

```Csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

EF コアは、これらの型のインスタンスを作成するときなど、クエリの結果をまず既定のパラメーターなしコンス トラクターを呼び出すされ値をデータベースの各プロパティを設定します。 ただし、EF コアでパラメーター化されたコンス トラクターが見つかった場合パラメーターの名前との一致する型のプロパティをマップしてから、それらのプロパティの値を持つパラメーター化されたコンス トラクターを呼び出す代わりには、各プロパティを明示的に設定できません。 例:

```Csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
いくつかの点に注意してください。
* すべてのプロパティには、コンス トラクターのパラメーターが必要です。 たとえば、Post.Content プロパティが設定されていないのコンス トラクター パラメーターで EF コアを使用すると通常の方法で、コンス トラクターの呼び出し後に設定されます。
* パラメーターは、camel 形式の中に、プロパティを pascal でく点を除いて、パラメーターの型と名前がプロパティの型と名前になければなりません。
* (ブログ投稿上など) のナビゲーション プロパティは設定できません EF コア コンス トラクターを使用します。
* コンス トラクターをパブリックにすることができます、プライベート、またはその他の任意のアクセシビリティがします。

### <a name="read-only-properties"></a>読み取り専用プロパティ

コンス トラクターを使用して設定されるプロパティとできます賢明、一部の読み取り専用にします。 EF コアが、これをサポートしますが、いくつかの点を注意してください。
* プロパティ set アクセス操作子なしでは、規約によってマップされていません。 (これにより傾向がありますにはない対応する、計算済みプロパティなどのプロパティをマップします。)
* 自動的に生成されたキー値を使用するには、キーの値は、新しいエンティティを挿入するときに、キー生成コンポーネントに設定する必要があるため、読み取り/書き込みであるキー プロパティが必要です。

次の作業を回避する簡単な方法では、プライベート set アクセス操作子を使用します。 例:
```Csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}

```
EF Core では、以前と同様にマップされているすべてのプロパティと、キーができることを意味するストアで生成された、読み取り/書き込みとしてプライベート set アクセス操作子を持つプロパティが表示されます。

プライベート set アクセス操作子を使用する代わりにを本当に読み取り専用プロパティを作成し、OnModelCreating により明示的なマッピングを追加します。 同様に、いくつかのプロパティを完全に削除およびフィールドだけに置き換えられます。 たとえば、これらのエンティティ型があるとします。

```Csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```
および OnModelCreating でこの構成:
```Csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```
点に注意してください。
* "Property"、キーは、今すぐフィールドです。 `readonly`ストア生成のキーを使用できるようにするフィールドです。 
* その他のプロパティは、コンス トラクターでのみ設定する読み取り専用のプロパティです。
* 主キーの値をデータベースからの読み取り、または EF によって設定だけ場合、コンス トラクターに含める必要はありますありません。 これは、単純なフィールドとして、キー"property"のままにし、オフにする必要がありますいないを設定するために明示的に新しいブログまたは投稿を作成するときになります。

> [!NOTE]  
> このコードは、コンパイラの警告 '169' を示す、フィールドが使用されないことになります。 実際に EF コアを使用いるため、フィールド extralinguistic 方法では無視できます。

## <a name="injecting-services"></a>サービスを提供します。

EF コアは、エンティティ型のコンス トラクターに「サービス」を挿入することができますも。 たとえば、次を挿入できます。
* `DbContext` -現在のコンテキストのインスタンスは、派生 DbContext 型として型指定することができますも
* `ILazyLoader` --、遅延読み込みサービスを参照してください、[遅延読み込みドキュメント](../querying/related-data.md)詳細については
* `Action<object, string>` -遅延読み込みデリゲート--を参照してください、[遅延読み込みドキュメント](../querying/related-data.md)詳細については
* `IEntityType` -このエンティティ型に関連付けられている EF コア メタデータ

> [!NOTE]  
> EF コア 2.1 では、時点で既知の EF のコアでのサービスだけを挿入できます。 今後のリリースでは、アプリケーション サービスを提供するためのサポートを検討しています。

たとえば、挿入された DbContext は、選択的にそれらのすべてを読み込むことがなく、関連エンティティに関する情報を取得するデータベースへのアクセスに使用できます。 次の例では、これは、投稿を読み込むことがなくブログの投稿の数の取得に使用しています。

```Csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
これについて注意するいくつかの点:
* のみである EF のコアでを呼び出すことと、一般的な用途の別のパブリック コンス トラクターがあるため、コンス トラクターはプライベートであります。
* 挿入されたサービスを使用して、コード (つまり、コンテキスト) がこれに対する防御されて`null`EF コア、インスタンスの作成がないケースを処理します。
* サービスが、読み取り/書き込みプロパティに格納されているために、エンティティがコンテキストの新しいインスタンスに接続されている場合にリセットされます。

> [!WARNING]  
> 次のように DbContext を挿入することは多くの場合と見なされますアンチ パターン EF コアに直接、エンティティ型ではそのためです。 次のようにサービスの挿入を使用する前にすべてのオプションを慎重に検討します。
