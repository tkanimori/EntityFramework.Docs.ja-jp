---
title: テーブルマッピング-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 474c49aca4c65cd5d58b184b1f3c2d30e7abff84
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656098"
---
# <a name="table-mapping"></a>テーブル マッピング

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

テーブルマッピングは、データベース内のどのテーブルデータに対してクエリを行って保存するかを指定します。

## <a name="conventions"></a>規約

規則では、派生コンテキストでエンティティを公開する `DbSet<TEntity>` プロパティと同じ名前を持つテーブルにマッピングされるように各エンティティが設定されます。 特定のエンティティに `DbSet<TEntity>` が含まれていない場合は、クラス名が使用されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、型がマップされるテーブルを構成できます。

``` csharp
using System.ComponentModel.DataAnnotations.Schema;

[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

また、テーブルが属するスキーマを指定することもできます。

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、型がマップされるテーブルを構成することができます。

``` csharp
using Microsoft.EntityFrameworkCore;

class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .ToTable("blogs");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

また、テーブルが属するスキーマを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/TableAndSchema.cs?name=Table&highlight=2)]
