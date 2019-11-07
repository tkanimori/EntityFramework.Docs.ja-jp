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
# <a name="table-mapping"></a><span data-ttu-id="d8cd8-102">テーブル マッピング</span><span class="sxs-lookup"><span data-stu-id="d8cd8-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="d8cd8-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="d8cd8-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="d8cd8-105">テーブルマッピングは、データベース内のどのテーブルデータに対してクエリを行って保存するかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="d8cd8-106">規約</span><span class="sxs-lookup"><span data-stu-id="d8cd8-106">Conventions</span></span>

<span data-ttu-id="d8cd8-107">規則では、派生コンテキストでエンティティを公開する `DbSet<TEntity>` プロパティと同じ名前を持つテーブルにマッピングされるように各エンティティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="d8cd8-108">特定のエンティティに `DbSet<TEntity>` が含まれていない場合は、クラス名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d8cd8-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="d8cd8-109">Data Annotations</span></span>

<span data-ttu-id="d8cd8-110">データ注釈を使用して、型がマップされるテーブルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;

[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="d8cd8-111">また、テーブルが属するスキーマを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="d8cd8-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d8cd8-112">Fluent API</span></span>

<span data-ttu-id="d8cd8-113">Fluent API を使用して、型がマップされるテーブルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="d8cd8-114">また、テーブルが属するスキーマを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d8cd8-114">You can also specify a schema that the table belongs to.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/TableAndSchema.cs?name=Table&highlight=2)]
