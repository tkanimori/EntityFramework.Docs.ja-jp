---
title: シャドウのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 5fdc4c50c295f73d0fa5eef3518adf4d3eb95599
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197703"
---
# <a name="shadow-properties"></a>シャドウ プロパティ

Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。 これらのプロパティの値と状態は、単に変更トラッカーに保持されます。

シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。 2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理される外部キープロパティで最もよく使用されます。

シャドウプロパティの値は、API を`ChangeTracker`使用して取得および変更できます。

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

シャドウプロパティは、静的メソッドを使用して`EF.Property` LINQ クエリで参照できます。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>規約

リレーションシップが検出されたが、依存エンティティクラスで外部キープロパティが見つからない場合は、規則によってシャドウプロパティを作成できます。 この場合、shadow 外部キープロパティが導入されます。 Shadow 外部キーのプロパティにはと`<navigation property name><principal key property name>`いう名前が付けられます (これは、プリンシパルエンティティをポイントする依存エンティティのナビゲーションで、名前付けに使用されます)。 プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前はだけ`<principal key property name>`になります。 依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。

たとえば、次のコードリストでは、 `BlogId`シャドウプロパティが`Post`エンティティに導入されます。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/ShadowForeignKey.cs)] -->
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

シャドウプロパティをデータ注釈と共に作成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用してシャドウプロパティを構成できます。 の`Property`文字列オーバーロードを呼び出した後は、他のプロパティに対する任意の構成呼び出しをチェーンできます。

`Property`メソッドに指定された名前が既存のプロパティの名前 (shadow プロパティまたは entity クラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/ShadowProperty.cs?highlight=7,8)] -->
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
