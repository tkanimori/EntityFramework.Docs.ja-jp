---
title: 主キーの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
ms.technology: entity-framework-core
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: fcb1871149c0f20a2576864028b4171904de1982
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052722"
---
# <a name="primary-keys"></a><span data-ttu-id="c27cf-102">主キー</span><span class="sxs-lookup"><span data-stu-id="c27cf-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="c27cf-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c27cf-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c27cf-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="c27cf-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c27cf-105">主キー制約には、各エンティティの種類のキーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="c27cf-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="c27cf-106">規則</span><span class="sxs-lookup"><span data-stu-id="c27cf-106">Conventions</span></span>

<span data-ttu-id="c27cf-107">データベースの主キーの名前付け規則、により`PK_<type name>`です。</span><span class="sxs-lookup"><span data-stu-id="c27cf-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c27cf-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c27cf-108">Data Annotations</span></span>

<span data-ttu-id="c27cf-109">主キーの特定の側面をリレーショナル データベース構成できますありませんデータ注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="c27cf-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c27cf-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c27cf-110">Fluent API</span></span>

<span data-ttu-id="c27cf-111">Fluent API を使用すると、データベースに主キー制約の名前を構成します。</span><span class="sxs-lookup"><span data-stu-id="c27cf-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

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
