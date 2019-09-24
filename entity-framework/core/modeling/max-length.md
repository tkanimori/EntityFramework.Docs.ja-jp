---
title: 最大長-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: b6f0594fed0c491b4f79dcda5273cdebe9ecf35f
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197227"
---
# <a name="maximum-length"></a><span data-ttu-id="1747e-102">最大長</span><span class="sxs-lookup"><span data-stu-id="1747e-102">Maximum Length</span></span>

<span data-ttu-id="1747e-103">最大長を構成すると、特定のプロパティに対して使用する適切なデータ型に関するヒントがデータストアに提供されます。</span><span class="sxs-lookup"><span data-stu-id="1747e-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="1747e-104">最大長は、配列のデータ型 ( `string`や`byte[]`など) にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="1747e-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="1747e-105">Entity Framework は、データをプロバイダーに渡す前に、最大長の検証を行いません。</span><span class="sxs-lookup"><span data-stu-id="1747e-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="1747e-106">必要に応じて、プロバイダーまたはデータストアが検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1747e-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="1747e-107">たとえば、SQL Server を対象とする場合、基になる列のデータ型では過剰なデータを格納できないため、最大長を超えると例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="1747e-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="1747e-108">規約</span><span class="sxs-lookup"><span data-stu-id="1747e-108">Conventions</span></span>

<span data-ttu-id="1747e-109">慣例により、プロパティに適切なデータ型を選択するために、データベースプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1747e-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="1747e-110">長さがのプロパティの場合、データベースプロバイダーは通常、データの長さが最も長いデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="1747e-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="1747e-111">たとえば、Microsoft SQL Server はプロパティに`nvarchar(max)` `string`を使用します`nvarchar(450)` (または、列がキーとして使用されている場合)。</span><span class="sxs-lookup"><span data-stu-id="1747e-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="1747e-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="1747e-112">Data Annotations</span></span>

<span data-ttu-id="1747e-113">データ注釈を使用して、プロパティの最大長を構成できます。</span><span class="sxs-lookup"><span data-stu-id="1747e-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="1747e-114">この例では、この SQL Server を対象とする`nvarchar(500)`と、データ型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1747e-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="1747e-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1747e-115">Fluent API</span></span>

<span data-ttu-id="1747e-116">Fluent API を使用して、プロパティの最大長を構成できます。</span><span class="sxs-lookup"><span data-stu-id="1747e-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="1747e-117">この例では、この SQL Server を対象とする`nvarchar(500)`と、データ型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1747e-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
