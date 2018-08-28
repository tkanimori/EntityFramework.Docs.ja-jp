---
title: シャドウ プロパティ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b7b7b10642564dfa3dbc05755188b5b5c63e0d03
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993803"
---
# <a name="shadow-properties"></a>シャドウ プロパティ

シャドウ プロパティは、.NET のエンティティ クラスで定義されていないが、EF Core モデルでは、そのエンティティ型に対して定義されているプロパティ。 純粋な変更の追跡ツールでは、値とこれらのプロパティの状態が維持されます。

シャドウ プロパティは、マップされたエンティティ型で公開してはなりませんが、データベース内のデータがある場合に便利です。 外部キー プロパティ、2 つのエンティティ間のリレーションシップは、データベース内の外部キー値によって表されますが、リレーションシップがエンティティ型の間のナビゲーション プロパティを使用して、エンティティ型で管理されている、最もよく使用されます。

シャドウ プロパティの値を入手して使用して変更することができます、 `ChangeTracker` API。

``` csharp
   context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

シャドウ プロパティを使用した LINQ クエリで参照できます、`EF.Property`静的メソッド。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>規約

リレーションシップが検出されますが、依存エンティティ クラスの外部キー プロパティが見つからないときに、慣例シャドウ プロパティを作成できます。 この場合、影の外部キー プロパティが導入されます。 シャドウの外部キー プロパティの名前は`<navigation property name><principal key property name>`(プリンシパル エンティティを指す、依存エンティティのナビゲーションは、名前付けの使用)。 かどうかには、プリンシパルのキー プロパティの名前には、ナビゲーション プロパティの名前が含まれていますし、名前になります`<principal key property name>`します。 依存エンティティにナビゲーション プロパティがない場合、プリンシパルの種類名は、その代わりに使用されます。

たとえば、次のコード リストになります。、`BlogId`シャドウ プロパティがに導入される、`Post`エンティティ。

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

シャドウ プロパティを作成して、データ注釈を使用したことはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、シャドウ プロパティを構成することができます。 文字列のオーバー ロードを呼び出した後`Property`他のプロパティの構成の呼び出しを連結することができます。

名前が指定されている場合、`Property`メソッド (シャドウ プロパティまたはエンティティ クラスで定義されているいずれか)、既存のプロパティの名前に一致し、コードでは新しいシャドウ プロパティを導入するのではなく、その既存のプロパティを設定します。

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
