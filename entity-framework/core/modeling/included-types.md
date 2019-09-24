---
title: '& 型を除外する (EF Core)'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: ca83b1c432bdf4853dba81e12ec4a739bc8218dc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197377"
---
# <a name="including--excluding-types"></a>種類を含める/除外する

モデルに型を含めると、EF にはその型に関するメタデータが含まれ、データベースとの間でインスタンスの読み取りおよび書き込みが試行されます。

## <a name="conventions"></a>規約

慣例により、コンテキストのプロパティで`DbSet`公開される型は、モデルに含まれます。 さらに、 `OnModelCreating`メソッドに記述されている型も含まれています。 最後に、検出された型のナビゲーションプロパティを再帰的に調べることによって検出された型も、モデルに含まれます。

**たとえば、次のコードでは、3種類すべてが検出されています。**

* `Blog`コンテキストの`DbSet`プロパティで公開されているため

* `Post`ナビゲーションプロパティを使用し`Blog.Posts`て検出されるため、

* `AuditEntry`これは、「」で説明されています。`OnModelCreating`

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/IncludedTypes.cs?highlight=3,7,16)] -->
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

データ注釈を使用して、モデルから型を除外できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデルから型を除外できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
