---
title: テーブルのマッピング - EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
ms.technology: entity-framework-core
uid: core/modeling/relational/tables
ms.openlocfilehash: 73957d9c77e6856bfb65e10e6b373c337101f7d9
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052732"
---
# <a name="table-mapping"></a>テーブルのマッピング

> [!NOTE]  
> このセクションの構成は、一般にリレーショナル データベースに適用されます。 ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。

テーブル マッピングは、テーブル データをからクエリし、データベース内に保存する必要がありますを識別します。

## <a name="conventions"></a>規則

慣例により、各エンティティになりますセットアップと同じ名前のテーブルにマップする、`DbSet<TEntity>`派生のコンテキストにエンティティを公開するプロパティです。 ない場合は`DbSet<TEntity>`が含まれる指定されたエンティティのクラス名を使用します。

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

このテーブルに所属するスキーマを指定することができます。

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

このテーブルに所属するスキーマを指定することができます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
