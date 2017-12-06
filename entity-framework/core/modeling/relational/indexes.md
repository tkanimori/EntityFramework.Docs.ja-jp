---
title: "インデックスの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: 683b580bb155e0416f13c5d63e3280078fbcee21
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="indexes"></a><span data-ttu-id="6c955-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="6c955-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="6c955-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6c955-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6c955-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="6c955-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6c955-105">リレーショナル データベース内のインデックスは、Entity Framework のコアでのインデックスと同じ概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="6c955-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="6c955-106">規則</span><span class="sxs-lookup"><span data-stu-id="6c955-106">Conventions</span></span>

<span data-ttu-id="6c955-107">慣例により、インデックスが名前付き`IX_<type name>_<property name>`します。</span><span class="sxs-lookup"><span data-stu-id="6c955-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="6c955-108">複合インデックスの`<property name>`プロパティ名のアンダー スコアの区切られたリストになります。</span><span class="sxs-lookup"><span data-stu-id="6c955-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6c955-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6c955-109">Data Annotations</span></span>

<span data-ttu-id="6c955-110">インデックスは、データ注釈を使用していない構成できます。</span><span class="sxs-lookup"><span data-stu-id="6c955-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6c955-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6c955-111">Fluent API</span></span>

<span data-ttu-id="6c955-112">Fluent API を使用すると、インデックスの名前を構成します。</span><span class="sxs-lookup"><span data-stu-id="6c955-112">You can use the Fluent API to configure the name of an index.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/IndexName.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .HasName("Index_Url");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
