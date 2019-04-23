---
title: 含める/除外する型 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: f533b24312af37634ce4957e43c39ce776bf0bf0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929798"
---
# <a name="including--excluding-types"></a>含める/除外する型

種類を含むにモデルを EF がに関するメタデータを持つ入力し、読み取りし、書き込みデータベースのインスタンスを試みます。

## <a name="conventions"></a>規約

慣例により、型で公開されている`DbSet`プロパティのコンテキストで、モデルに含まれます。 さらに記載されている型、`OnModelCreating`メソッドも含まれています。 最後に、検出された型のナビゲーション プロパティの調査を再帰的にある任意の型は、モデルにも含まれます。

**たとえば、次のコード リストですべての 3 種類が検出されます。**

* `Blog` 公開されているため、`DbSet`コンテキストのプロパティ

* `Post` 使用して検出されたため、`Blog.Posts`ナビゲーション プロパティ

* `AuditEntry` 指定されているため、 `OnModelCreating`

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
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

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、モデルから型を除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデルから型を除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=12)]
