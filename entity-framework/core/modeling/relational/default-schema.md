---
title: EF Core - 既定のスキーマ
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 800551bbadd0a9e8b5eb7070a8ccf6ed2407e3d2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995367"
---
# <a name="default-schema"></a><span data-ttu-id="cb000-102">既定のスキーマ</span><span class="sxs-lookup"><span data-stu-id="cb000-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="cb000-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cb000-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="cb000-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="cb000-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="cb000-105">既定のスキーマは、そのオブジェクトのスキーマが明示的に構成されていない場合にオブジェクトを作成するデータベース スキーマです。</span><span class="sxs-lookup"><span data-stu-id="cb000-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="cb000-106">規約</span><span class="sxs-lookup"><span data-stu-id="cb000-106">Conventions</span></span>

<span data-ttu-id="cb000-107">慣例により、データベース プロバイダーは、最も適切な既定のスキーマを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb000-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="cb000-108">たとえば、Microsoft SQL Server が使用して、`dbo`スキーマと SQLite されますので使用しませんスキーマ (スキーマは、SQLite ではサポートされていない)。</span><span class="sxs-lookup"><span data-stu-id="cb000-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="cb000-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="cb000-109">Data Annotations</span></span>

<span data-ttu-id="cb000-110">データ注釈を使用して既定のスキーマを設定することができません。</span><span class="sxs-lookup"><span data-stu-id="cb000-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="cb000-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="cb000-111">Fluent API</span></span>

<span data-ttu-id="cb000-112">Fluent API を使用すると、既定のスキーマを指定します。</span><span class="sxs-lookup"><span data-stu-id="cb000-112">You can use the Fluent API to specify a default schema.</span></span>

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
