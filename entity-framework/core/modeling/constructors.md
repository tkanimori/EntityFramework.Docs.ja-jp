---
title: EF Core のコンス トラクターを使用したエンティティ型
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 5bf49718f02c1860871b1f4c255ec4d98fce2fc7
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405245"
---
# <a name="entity-types-with-constructors"></a>コンス トラクターを使用したエンティティ型

> [!NOTE]  
> これは EF Core 2.1 の新機能です。

EF Core 2.1 以降の場合は、パラメーターを持つコンス トラクターを定義し、EF core がエンティティのインスタンスを作成するときに、このコンス トラクターを呼び出すようになりました。 コンス トラクターのパラメーターは、マップされたプロパティにバインドできるまたは動作などの遅延読み込みを容易にするサービスのさまざまな種類にします。

> [!NOTE]  
> EF Core 2.1 の時点では、コンス トラクターのすべてのバインドは、規則ことです。 使用する特定のコンス トラクターの構成は、将来のリリース予定です。

## <a name="binding-to-mapped-properties"></a>マップされたプロパティへのバインド

一般的なブログの投稿/モデルを検討してください。

``` csharp
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

EF Core では、これらの型のインスタンスを作成するときなど、クエリの結果を最初に既定のパラメーターなしコンス トラクターを呼び出すし、値に、データベースからの各プロパティを設定します。 ただし、EF Core でパラメーター化されたコンス トラクターが見つかった場合は、パラメーターの名前との一致する型のプロパティをマップし、それらのプロパティの値を持つパラメーター化されたコンス トラクターを呼び出す代わりには、各プロパティを明示的に設定しません。 例えば:

``` csharp
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
* すべてのプロパティは、コンス トラクターのパラメーターを指定する必要があります。 たとえば、ため、EF Core を使用すると、通常どおりに、コンス トラクターを呼び出した後設定は、コンス トラクター パラメーターで Post.Content プロパティは設定されません。
* パラメーターは、camel 形式の中に、プロパティを pascal 形式で表記できますが、パラメーターの型と名前がプロパティの型と名前にする必要があります一致します。
* EF Core がナビゲーション プロパティ (ブログや投稿上記) を設定できませんコンス トラクターを使用します。
* コンス トラクターはパブリックであることができます、プライベート、またはその他の任意のアクセシビリティを持ちます。 ただし、遅延読み込みプロキシでは、コンス トラクターは、継承するプロキシ クラスからアクセスできることが必要です。 つまり通常、public または protected のいずれかになります。

### <a name="read-only-properties"></a>読み取り専用プロパティ

コンス トラクターを使用してプロパティが設定されていると合理的に一部の読み取り専用です。 これには、EF Core をサポートしていますが、注意すべきいくつかの点があります。
* プロパティ set アクセス操作子なしは規約によってマップされていません。 (これにより、計算されたプロパティなど、マップしていないプロパティをマップする傾向)。
* 自動的に生成されたキーの値を使用して、キー プロパティ、キーの値は、新しいエンティティを挿入するときにキー生成コンポーネントによって設定する必要があるため、読み取り/書き込みである必要があります。

これらの操作を回避する簡単な方法では、プライベート セッターを使用します。 例えば:
``` csharp
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
EF Core には、読み取り/書き込み、つまり、すべてのプロパティが以前と同様にマップされ、キーこともできますがストア生成としてプライベート セッターを持つプロパティが表示されます。

プライベート セッターを使用する代わりに実際に読み取り専用プロパティを作成し、OnModelCreating でより明確なマッピングを追加することです。 同様に、いくつかのプロパティを完全に削除し、フィールドだけに置き換えられます。 たとえば、これらのエンティティ型があるとします。

``` csharp
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
OnModelCreating では、この構成:
``` csharp
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
* "Property"、キーは、今すぐフィールドです。 `readonly`フィールド ストア生成キーを使用できるようにします。
* その他のプロパティは、コンス トラクターでのみ設定する読み取り専用のプロパティです。
* 主キーの値を EF によって設定しかまたはデータベースからの読み取りは、コンス トラクターに含める必要はあるありません。 これは、操作を行うと、"property"、キーを単純なフィールドとしてなりに設定してはならない明示的に新しいブログや投稿を作成するときにことを明確になります。

> [!NOTE]  
> このコードは、コンパイラの警告 '169' を示すフィールドは使用しないことになります。 これは、実際には EF Core が extralinguistic の方法で、フィールドを使用しているので無視できます。

## <a name="injecting-services"></a>サービスを挿入します。

EF Core は、エンティティ型のコンス トラクターに「サービス」を挿入することができますも。 たとえば、次を挿入できます。
* `DbContext` -現在のコンテキストのインスタンスは、DbContext 派生型として型指定することもできます
* `ILazyLoader` -遅延読み込みサービス--を参照してください、[遅延読み込みドキュメント](../querying/related-data.md)の詳細
* `Action<object, string>` -- 遅延読み込みデリゲートを参照してください、[遅延読み込みドキュメント](../querying/related-data.md)の詳細
* `IEntityType` -このエンティティ型に関連付けられている EF Core のメタデータ

> [!NOTE]  
> EF Core 2.1 の時点では、EF Core によって認識されているサービスのみを挿入できます。 今後のリリースでは、アプリケーション サービスを挿入するためのサポートを検討しています。

たとえば、選択的にそれらすべてを読み込むことがなく、関連エンティティに関する情報を取得するデータベースにアクセスする挿入の DbContext を使用できます。 次の例では、これは、投稿を読み込むことがなく、ブログの投稿の数の取得に使用しています。

``` csharp
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
これについて注意する少数です。
* EF Core でしかと呼ばれ、一般的な用途の別のパブリック コンス トラクターがあるために、コンス トラクターはプライベートでします。
* 挿入されたサービス (コンテキスト) を使用したコードはこれに対する防御されて`null`EF Core インスタンスの作成がない場合を処理します。
* サービスが、読み取り/書き込みプロパティに格納されているため、エンティティが新しいコンテキスト インスタンスにアタッチされているときにリセットされます。

> [!WARNING]  
> 次のように DbContext を挿入する多くの場合と見なされますアンチ パターンから EF Core に直接、エンティティ型が関連付けられています。 このようなサービスの挿入を使用する前にすべてのオプションを慎重に検討します。
