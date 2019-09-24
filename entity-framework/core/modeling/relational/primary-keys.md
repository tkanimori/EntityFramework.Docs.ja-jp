---
title: 主キー-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: bdb31964d717c64bddc28e7f1ce55b261e285a9b
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196955"
---
# <a name="primary-keys"></a><span data-ttu-id="c2920-102">主キー</span><span class="sxs-lookup"><span data-stu-id="c2920-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="c2920-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c2920-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c2920-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c2920-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c2920-105">主キー制約は、各エンティティ型のキーに対して導入されています。</span><span class="sxs-lookup"><span data-stu-id="c2920-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="c2920-106">規約</span><span class="sxs-lookup"><span data-stu-id="c2920-106">Conventions</span></span>

<span data-ttu-id="c2920-107">慣例により、データベースの主キーにはという`PK_<type name>`名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="c2920-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c2920-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c2920-108">Data Annotations</span></span>

<span data-ttu-id="c2920-109">データ注釈を使用して、主キーのリレーショナルデータベース固有の側面を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c2920-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c2920-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c2920-110">Fluent API</span></span>

<span data-ttu-id="c2920-111">Fluent API を使用して、データベース内の primary key 制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c2920-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/KeyName.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasKey(b => b.BlogId)
            .HasName("PrimaryKey_BlogId");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
