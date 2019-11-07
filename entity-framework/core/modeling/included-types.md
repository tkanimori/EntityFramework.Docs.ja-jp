---
title: '& 型を除外する (EF Core)'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: 1249e71c02e58afe7fe06b3fdcf523dfa0c9b17c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655741"
---
# <a name="including--excluding-types"></a><span data-ttu-id="504da-102">種類を含める/除外する</span><span class="sxs-lookup"><span data-stu-id="504da-102">Including & Excluding Types</span></span>

<span data-ttu-id="504da-103">モデルに型を含めると、EF にはその型に関するメタデータが含まれ、データベースとの間でインスタンスの読み取りおよび書き込みが試行されます。</span><span class="sxs-lookup"><span data-stu-id="504da-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="504da-104">規約</span><span class="sxs-lookup"><span data-stu-id="504da-104">Conventions</span></span>

<span data-ttu-id="504da-105">慣例により、コンテキストの `DbSet` プロパティで公開される型は、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="504da-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="504da-106">さらに、`OnModelCreating` メソッドに記述されている型も含まれます。</span><span class="sxs-lookup"><span data-stu-id="504da-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="504da-107">最後に、検出された型のナビゲーションプロパティを再帰的に調べることによって検出された型も、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="504da-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="504da-108">**たとえば、次のコードでは、3種類すべてが検出されています。**</span><span class="sxs-lookup"><span data-stu-id="504da-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="504da-109">コンテキストの `DbSet` プロパティで公開されているため `Blog`</span><span class="sxs-lookup"><span data-stu-id="504da-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="504da-110">`Blog.Posts` ナビゲーションプロパティを使用して検出されるため `Post`</span><span class="sxs-lookup"><span data-stu-id="504da-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="504da-111">`AuditEntry` は `OnModelCreating` に記載されています。</span><span class="sxs-lookup"><span data-stu-id="504da-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/IncludedTypes.cs?name=IncludedTypes&highlight=3,7,16)]

## <a name="data-annotations"></a><span data-ttu-id="504da-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="504da-112">Data Annotations</span></span>

<span data-ttu-id="504da-113">データ注釈を使用して、モデルから型を除外できます。</span><span class="sxs-lookup"><span data-stu-id="504da-113">You can use Data Annotations to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a><span data-ttu-id="504da-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="504da-114">Fluent API</span></span>

<span data-ttu-id="504da-115">Fluent API を使用して、モデルから型を除外できます。</span><span class="sxs-lookup"><span data-stu-id="504da-115">You can use the Fluent API to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
