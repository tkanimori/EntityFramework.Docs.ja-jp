---
title: プライマリ キー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: 916f3adbcd08cb1037c7fbf68e99630feb321a61
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998069"
---
# <a name="primary-keys"></a><span data-ttu-id="8a652-102">主キー</span><span class="sxs-lookup"><span data-stu-id="8a652-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="8a652-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a652-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="8a652-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8a652-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="8a652-105">主キー制約には、各エンティティ型のキーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="8a652-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="8a652-106">規約</span><span class="sxs-lookup"><span data-stu-id="8a652-106">Conventions</span></span>

<span data-ttu-id="8a652-107">データベースの主キーの名前付け規則、により`PK_<type name>`します。</span><span class="sxs-lookup"><span data-stu-id="8a652-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8a652-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8a652-108">Data Annotations</span></span>

<span data-ttu-id="8a652-109">データ注釈に構成する主キーの特定の側面をリレーショナル データベースがない場合。</span><span class="sxs-lookup"><span data-stu-id="8a652-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="8a652-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8a652-110">Fluent API</span></span>

<span data-ttu-id="8a652-111">Fluent API を使用して、データベースで主キー制約の名前を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8a652-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/KeyName.cs?highlight=9)] -->
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
