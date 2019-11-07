---
title: インデックス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: d1b5cd6853cd24f7e1aa3aace2f0a3455c657cc1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655698"
---
# <a name="indexes"></a><span data-ttu-id="71b99-102">Indexes</span><span class="sxs-lookup"><span data-stu-id="71b99-102">Indexes</span></span>

<span data-ttu-id="71b99-103">インデックスは、多くのデータストアで共通の概念です。</span><span class="sxs-lookup"><span data-stu-id="71b99-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="71b99-104">データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71b99-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="71b99-105">規約</span><span class="sxs-lookup"><span data-stu-id="71b99-105">Conventions</span></span>

<span data-ttu-id="71b99-106">規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="71b99-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="71b99-107">データの注釈</span><span class="sxs-lookup"><span data-stu-id="71b99-107">Data Annotations</span></span>

<span data-ttu-id="71b99-108">データ注釈を使用してインデックスを作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="71b99-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="71b99-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="71b99-109">Fluent API</span></span>

<span data-ttu-id="71b99-110">Fluent API を使用して、1つのプロパティにインデックスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="71b99-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="71b99-111">既定では、インデックスは一意ではありません。</span><span class="sxs-lookup"><span data-stu-id="71b99-111">By default, indexes are non-unique.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=7,8)]

<span data-ttu-id="71b99-112">また、インデックスが一意であることを指定することもできます。つまり、2つのエンティティが特定のプロパティに対して同じ値を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="71b99-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=ModelBuilder&highlight=3)]

<span data-ttu-id="71b99-113">複数の列に対してインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="71b99-113">You can also specify an index over more than one column.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=7,8)]

> [!TIP]  
> <span data-ttu-id="71b99-114">個別のプロパティセットごとにインデックスが1つだけ存在します。</span><span class="sxs-lookup"><span data-stu-id="71b99-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="71b99-115">Fluent API を使用して、既にインデックスが定義されている一連のプロパティ (規則または以前の構成) のインデックスを構成する場合は、そのインデックスの定義を変更します。</span><span class="sxs-lookup"><span data-stu-id="71b99-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="71b99-116">これは、規則によって作成されたインデックスをさらに構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="71b99-116">This is useful if you want to further configure an index that was created by convention.</span></span>
