---
title: テーブルのマッピング - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
ms.technology: entity-framework-core
uid: core/modeling/relational/tables
ms.openlocfilehash: ef6080b5d543c2a68a680be2b9effc1c9d531030
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949010"
---
# <a name="table-mapping"></a>テーブル マッピング

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

テーブル マッピングは、テーブル データをからクエリを実行し、データベース内に保存する必要がありますを識別します。

## <a name="conventions"></a>規約

慣例により、各エンティティして設定されると、同じ名前のテーブルにマップする、`DbSet<TEntity>`派生コンテキストでエンティティを公開するプロパティ。 ない場合は`DbSet<TEntity>`が含まれる指定されたエンティティのクラス名を使用します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、型にマップするテーブルを構成することができます。

``` csharp
using System.ComponentModel.DataAnnotations.Schema;
```
``` csharp
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

テーブルが属するスキーマを指定することもできます。

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、型にマップするテーブルを構成することができます。

``` csharp
using Microsoft.EntityFrameworkCore;
```
``` csharp
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

テーブルが属するスキーマを指定することもできます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
