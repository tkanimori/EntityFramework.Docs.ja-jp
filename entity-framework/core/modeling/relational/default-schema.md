---
title: 既定のスキーマの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
ms.technology: entity-framework-core
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 26106deb2d4e35ecf33e97790a83f9af77991aed
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052752"
---
# <a name="default-schema"></a><span data-ttu-id="e3541-102">[既定のスキーマ]</span><span class="sxs-lookup"><span data-stu-id="e3541-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="e3541-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e3541-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="e3541-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="e3541-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="e3541-105">既定のスキーマは、そのオブジェクトのスキーマが明示的に構成されていない場合にオブジェクトを作成するデータベースのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="e3541-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="e3541-106">規則</span><span class="sxs-lookup"><span data-stu-id="e3541-106">Conventions</span></span>

<span data-ttu-id="e3541-107">慣例により、データベース プロバイダーは、最も適切な既定のスキーマを選択します。</span><span class="sxs-lookup"><span data-stu-id="e3541-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="e3541-108">たとえば、Microsoft SQL Server が使用されます、`dbo`スキーマと SQLite は使用しませんスキーマ (からのスキーマは、SQLite ではサポートされていません)。</span><span class="sxs-lookup"><span data-stu-id="e3541-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="e3541-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="e3541-109">Data Annotations</span></span>

<span data-ttu-id="e3541-110">データ注釈を使用して既定のスキーマを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="e3541-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="e3541-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e3541-111">Fluent API</span></span>

<span data-ttu-id="e3541-112">Fluent API を使用すると、既定のスキーマを指定します。</span><span class="sxs-lookup"><span data-stu-id="e3541-112">You can use the Fluent API to specify a default schema.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultSchema.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("blogging");
    }
}
```
