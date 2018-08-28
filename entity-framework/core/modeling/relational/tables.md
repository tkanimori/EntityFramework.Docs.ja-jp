---
title: テーブルのマッピング - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 32c5e3cc0e498005ce8e6be1f1ee7e8ddf9b510d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994138"
---
# <a name="table-mapping"></a><span data-ttu-id="7ad53-102">テーブル マッピング</span><span class="sxs-lookup"><span data-stu-id="7ad53-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="7ad53-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7ad53-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="7ad53-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7ad53-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="7ad53-105">テーブル マッピングは、テーブル データをからクエリを実行し、データベース内に保存する必要がありますを識別します。</span><span class="sxs-lookup"><span data-stu-id="7ad53-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="7ad53-106">規約</span><span class="sxs-lookup"><span data-stu-id="7ad53-106">Conventions</span></span>

<span data-ttu-id="7ad53-107">慣例により、各エンティティして設定されると、同じ名前のテーブルにマップする、`DbSet<TEntity>`派生コンテキストでエンティティを公開するプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7ad53-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="7ad53-108">ない場合は`DbSet<TEntity>`が含まれる指定されたエンティティのクラス名を使用します。</span><span class="sxs-lookup"><span data-stu-id="7ad53-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7ad53-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="7ad53-109">Data Annotations</span></span>

<span data-ttu-id="7ad53-110">データ注釈を使用して、型にマップするテーブルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7ad53-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="7ad53-111">テーブルが属するスキーマを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="7ad53-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="7ad53-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7ad53-112">Fluent API</span></span>

<span data-ttu-id="7ad53-113">Fluent API を使用して、型にマップするテーブルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7ad53-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="7ad53-114">テーブルが属するスキーマを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="7ad53-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
