---
title: 必須/オプションのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 7200cd2eeeba2f22365ef09b1f50edd077240130
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149155"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="87ea2-102">必須および省略可能なプロパティ</span><span class="sxs-lookup"><span data-stu-id="87ea2-102">Required and Optional Properties</span></span>

<span data-ttu-id="87ea2-103">プロパティが有効`null`である場合、プロパティは省略可能と見なされます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="87ea2-104">が`null`プロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="87ea2-105">規約</span><span class="sxs-lookup"><span data-stu-id="87ea2-105">Conventions</span></span>

<span data-ttu-id="87ea2-106">慣例により、.net 型に null を含めることができるプロパティは、オプション`string`( `int?` `byte[]`、、など) として構成されます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-106">By convention, a property whose .NET type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="87ea2-107">CLR 型に null を含めることができないプロパティは、`int`必要`decimal`に`bool`応じて構成されます (、、など)。</span><span class="sxs-lookup"><span data-stu-id="87ea2-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="87ea2-108">.NET 型に null を含めることができないプロパティをオプションとして構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="87ea2-108">A property whose .NET type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="87ea2-109">プロパティは、常に Entity Framework によって要求されると見なされます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="87ea2-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="87ea2-110">Data Annotations</span></span>

<span data-ttu-id="87ea2-111">データ注釈を使用して、プロパティが必要であることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="87ea2-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="87ea2-112">Fluent API</span></span>

<span data-ttu-id="87ea2-113">Fluent API を使用して、プロパティが必要であることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="87ea2-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

