---
title: コンストラクターを使用したエンティティ型-EF Core
description: コンストラクターを使用して Entity Framework Core モデルとデータをバインドする
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 9502d75072eebb80c37cf1805e21f7d112269ba1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063713"
---
# <a name="entity-types-with-constructors"></a>コンストラクターを使用したエンティティ型

パラメーターを使用してコンストラクターを定義し、エンティティのインスタンスを作成するときにこのコンストラクターを呼び出す EF Core ことができます。 コンストラクターのパラメーターは、マップされたプロパティ、またはさまざまな種類のサービスにバインドして、遅延読み込みなどの動作を容易にすることができます。

> [!NOTE]
> 現在、すべてのコンストラクターのバインドは規約によって行います。 使用する特定のコンストラクターの構成は、将来のリリースで計画されています。

## <a name="binding-to-mapped-properties"></a>マップされたプロパティへのバインド

一般的なブログ/ポストモデルを考えてみましょう。

```csharp
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

EF Core は、クエリの結果など、これらの型のインスタンスを作成するときに、最初に既定のパラメーターなしのコンストラクターを呼び出し、次に各プロパティをデータベースの値に設定します。 ただし、パラメーター名と型がマップされたプロパティと一致するパラメーター化されたコンストラクターが EF Core によって検出された場合は、代わりに、これらのプロパティの値を使用してパラメーター化されたコンストラクターが呼び出され、各プロパティが明示的に設定されることはありません。 次に例を示します。

```csharp
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

注意事項:

* すべてのプロパティにコンストラクターパラメーターを設定する必要があるわけではありません。 たとえば、Post. Content プロパティは、コンストラクターのパラメーターによって設定されないため、EF Core はコンストラクターを通常の方法で呼び出した後に設定します。
* パラメーターの型と名前は、プロパティの型と名前を一致させる必要があります。ただし、パラメーターは、Pascal 形式ですが、パラメーターは camel 形式にすることができます。
* EF Core は、コンストラクターを使用して、ナビゲーションプロパティ (ブログや投稿など) を設定することはできません。
* コンストラクターは、パブリック、プライベート、またはその他のアクセシビリティを持つことができます。 ただし、レイジー読み込みプロキシでは、継承プロキシクラスからコンストラクターにアクセスできる必要があります。 通常、これはパブリックまたは保護されていることを意味します。

### <a name="read-only-properties"></a>読み取り専用プロパティ

コンストラクターを使用してプロパティを設定したら、それらの一部を読み取り専用にすることが理にかなっています。 EF Core はこれをサポートしていますが、次の点を確認してください。

* Setter のないプロパティは規約によってマップされません。 (これにより、計算されたプロパティなど、マップしないプロパティがマップされる傾向があります)。
* 自動的に生成されたキー値を使用するには、新しいエンティティを挿入するときにキージェネレーターによってキー値が設定される必要があるため、読み取り/書き込み可能なキープロパティが必要です。

これらの作業を回避する簡単な方法は、プライベート setter を使用することです。 次に例を示します。

```csharp
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

EF Core は、プライベート setter を持つプロパティを読み取り/書き込み可能として認識します。つまり、すべてのプロパティが以前としてマップされ、キーを格納して生成することができます。

プライベート setter を使用する代わりに、プロパティを実際に読み取り専用にし、OnModelCreating で明示的なマッピングを追加することもできます。 同様に、一部のプロパティを完全に削除し、フィールドのみに置き換えることができます。 たとえば、次のようなエンティティ型を考えてみます。

```csharp
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

また、OnModelCreating のこの構成は次のように作成されます。

```csharp
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

注意する点:

* キー "property" はフィールドになりました。 これは、ストアによって生成され `readonly` たキーを使用できるようにするためのフィールドではありません。
* その他のプロパティは、コンストラクターでのみ設定される読み取り専用プロパティです。
* 主キーの値が EF によってのみ設定された場合、またはデータベースから読み取られた場合は、それをコンストラクターに含める必要はありません。 これにより、キー "プロパティ" は単純なフィールドとして残され、新しいブログや投稿を作成するときに明示的に設定する必要がないことがわかります。

> [!NOTE]
> このコードでは、フィールドが使用されていないことを示すコンパイラ警告 ' 169 ' が生成されます。 実際には、extralinguistic の方法でフィールドが使用されているため、これは無視でき EF Core。

## <a name="injecting-services"></a>サービスの挿入

EF Core は、エンティティ型のコンストラクターに "services" を挿入することもできます。 たとえば、次のコードを挿入できます。

* `DbContext` -現在のコンテキストインスタンス。派生した DbContext 型として型指定することもできます。
* `ILazyLoader` -レイジー読み込みサービス-詳細については、 [遅延読み込みのドキュメント](xref:core/querying/related-data) を参照してください。
* `Action<object, string>` -遅延読み込みデリゲート-詳細については、 [遅延読み込みのドキュメント](xref:core/querying/related-data) を参照してください。
* `IEntityType` -このエンティティ型に関連付けられている EF Core メタデータ

> [!NOTE]
> 現時点では、EF Core によって認識されるサービスのみを挿入できます。 アプリケーションサービスの挿入のサポートは、今後のリリースで考慮されています。

たとえば、挿入された DbContext を使用して、データベースに選択的にアクセスし、それらのエンティティをすべて読み込まずに関連エンティティに関する情報を取得することができます。 次の例では、投稿を読み込まずにブログ内の投稿数を取得するために使用されています。

```csharp
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

これについては、次の点に注意してください。

* コンストラクターは EF Core によって呼び出されるだけなのでプライベートです。一般に使用するための別のパブリックコンストラクターもあります。
* 挿入されたサービスを使用するコード (つまり、コンテキスト) は、 `null` EF Core がインスタンスを作成していないケースを処理するために防御されます。
* サービスは読み取り/書き込みプロパティに格納されるため、エンティティが新しいコンテキストインスタンスにアタッチされるときにリセットされます。

> [!WARNING]
> このような DbContext の挿入は、エンティティ型を EF Core に直接結合するため、多くの場合、アンチパターンと見なされます。 このようなサービスインジェクションを使用する前に、すべてのオプションを慎重に検討してください。
