---
title: シャドウのプロパティ - EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
ms.technology: entity-framework-core
uid: core/modeling/shadow-properties
ms.openlocfilehash: 8c7f70789ddc6ebd24f9bfae931069834db593c2
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053552"
---
# <a name="shadow-properties"></a>プロパティをシャドウ

シャドウのプロパティは、.NET エンティティ クラスで定義されていないが、EF の主要なモデルでは、そのエンティティ型に対して定義されているプロパティです。 変更トラッカーの純粋値とこれらのプロパティの状態が維持されます。

シャドウのプロパティは、マップされているエンティティ型で公開する必要がないデータベース内のデータがある場合に便利です。 ほとんどの場合、データベースの外部キーの値で表される 2 つのエンティティ間のリレーションシップは、リレーションシップは、エンティティ型の間のナビゲーション プロパティを使用してエンティティの種類で管理されていますが、外部キー プロパティの使用されます。

シャドウ プロパティの値を入手して使用して変更することができます、 `ChangeTracker` API です。

``` csharp
   context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

プロパティをシャドウを使用した LINQ クエリで参照できる、`EF.Property`静的メソッドです。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>規則

リレーションシップが検出されますが、依存エンティティ クラスの外部キーのプロパティが見つからないときに、慣例によりシャドウのプロパティを作成できます。 この場合、シャドウ外部キーのプロパティが導入されます。 シャドウの外部キー プロパティの名前は`<navigation property name><principal key property name>`(プリンシパル エンティティを指す、依存エンティティのナビゲーションは、名前付けの使用)。 プリンシパル キー プロパティ名がナビゲーション プロパティの名前を含むかどうかは、名前は単なる`<principal key property name>`です。 依存エンティティにナビゲーション プロパティがない場合、プリンシパルの種類名は、代わりに使用されます。

たとえば、次のコード リストが結果の`BlogId`シャドウ プロパティに導入する際に、`Post`エンティティです。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a>データの注釈

データ注釈を使用したプロパティをシャドウを作成できません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、シャドウ プロパティを構成することができます。 文字列のオーバー ロードを呼び出した後`Property`他のプロパティは構成の呼び出しのいずれかにチェーンすることができます。

名前を指定する場合、`Property`メソッド (シャドウ プロパティまたはエンティティ クラスで定義されているいずれか)、既存のプロパティの名前に一致し、コードで新しいシャドウ プロパティを導入するのではなく、その既存のプロパティが設定されます。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
