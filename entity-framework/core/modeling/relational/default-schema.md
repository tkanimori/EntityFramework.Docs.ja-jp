---
title: 既定のスキーマ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: ae903ed7200859430aecc55073651236759bc6ce
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197129"
---
# <a name="default-schema"></a><span data-ttu-id="9ab43-102">既定のスキーマ</span><span class="sxs-lookup"><span data-stu-id="9ab43-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="9ab43-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="9ab43-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="9ab43-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="9ab43-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="9ab43-105">既定のスキーマは、そのオブジェクトに対してスキーマが明示的に構成されていない場合にオブジェクトが作成されるデータベーススキーマです。</span><span class="sxs-lookup"><span data-stu-id="9ab43-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="9ab43-106">規約</span><span class="sxs-lookup"><span data-stu-id="9ab43-106">Conventions</span></span>

<span data-ttu-id="9ab43-107">慣例により、データベースプロバイダーは最も適切な既定のスキーマを選択します。</span><span class="sxs-lookup"><span data-stu-id="9ab43-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="9ab43-108">たとえば、Microsoft SQL Server では`dbo`スキーマが使用され、sqlite ではスキーマが使用されません (スキーマは sqlite ではサポートされていないため)。</span><span class="sxs-lookup"><span data-stu-id="9ab43-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="9ab43-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="9ab43-109">Data Annotations</span></span>

<span data-ttu-id="9ab43-110">データ注釈を使用して既定のスキーマを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="9ab43-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="9ab43-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9ab43-111">Fluent API</span></span>

<span data-ttu-id="9ab43-112">Fluent API を使用して、既定のスキーマを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9ab43-112">You can use the Fluent API to specify a default schema.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultSchema.cs?highlight=7)] -->
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
