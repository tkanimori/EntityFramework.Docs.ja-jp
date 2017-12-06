---
title: "テーブルのマッピング - EF コア"
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
---
# <a name="table-mapping"></a><span data-ttu-id="cd02e-102">テーブルのマッピング</span><span class="sxs-lookup"><span data-stu-id="cd02e-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="cd02e-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="cd02e-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="cd02e-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="cd02e-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="cd02e-105">テーブル マッピングは、テーブル データをからクエリし、データベース内に保存する必要がありますを識別します。</span><span class="sxs-lookup"><span data-stu-id="cd02e-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="cd02e-106">規則</span><span class="sxs-lookup"><span data-stu-id="cd02e-106">Conventions</span></span>

<span data-ttu-id="cd02e-107">慣例により、各エンティティになりますセットアップと同じ名前のテーブルにマップする、`DbSet<TEntity>`派生のコンテキストにエンティティを公開するプロパティです。</span><span class="sxs-lookup"><span data-stu-id="cd02e-107">By convention, each entity will be setup to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="cd02e-108">ない場合は`DbSet<TEntity>`が含まれる指定されたエンティティのクラス名を使用します。</span><span class="sxs-lookup"><span data-stu-id="cd02e-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="cd02e-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="cd02e-109">Data Annotations</span></span>

<span data-ttu-id="cd02e-110">データ注釈を使用して、型にマップするテーブルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cd02e-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="cd02e-111">このテーブルに所属するスキーマを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="cd02e-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="cd02e-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="cd02e-112">Fluent API</span></span>

<span data-ttu-id="cd02e-113">Fluent API を使用して、型にマップするテーブルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cd02e-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="cd02e-114">このテーブルに所属するスキーマを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="cd02e-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
