---
title: 必須/任意のプロパティ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 564d9e62e2ed4f1a52b569630ed4994529e31dc1
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929811"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="8823f-102">必須およびオプションのプロパティ</span><span class="sxs-lookup"><span data-stu-id="8823f-102">Required and Optional Properties</span></span>

<span data-ttu-id="8823f-103">プロパティを含めるには有効な場合は省略可能なと見なされます`null`します。</span><span class="sxs-lookup"><span data-stu-id="8823f-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="8823f-104">場合`null`し、必要なプロパティであると見なされるプロパティに割り当てられる有効な値ではありません。</span><span class="sxs-lookup"><span data-stu-id="8823f-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

## <a name="conventions"></a><span data-ttu-id="8823f-105">規約</span><span class="sxs-lookup"><span data-stu-id="8823f-105">Conventions</span></span>

<span data-ttu-id="8823f-106">慣例により、プロパティの CLR 型が null を含めることができますが構成するオプションとして (`string`、 `int?`、`byte[]`など。)。</span><span class="sxs-lookup"><span data-stu-id="8823f-106">By convention, a property whose CLR type can contain null will be configured as optional (`string`, `int?`, `byte[]`, etc.).</span></span> <span data-ttu-id="8823f-107">CLR 型が null を含めることはできませんのプロパティを構成は必要に応じて (`int`、 `decimal`、`bool`など。)。</span><span class="sxs-lookup"><span data-stu-id="8823f-107">Properties whose CLR type cannot contain null will be configured as required (`int`, `decimal`, `bool`, etc.).</span></span>

> [!NOTE]  
> <span data-ttu-id="8823f-108">CLR 型を null に含めることはできませんプロパティは、省略可能として構成できません。</span><span class="sxs-lookup"><span data-stu-id="8823f-108">A property whose CLR type cannot contain null cannot be configured as optional.</span></span> <span data-ttu-id="8823f-109">常に Entity Framework によって必要なプロパティが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="8823f-109">The property will always be considered required by Entity Framework.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8823f-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8823f-110">Data Annotations</span></span>

<span data-ttu-id="8823f-111">データ注釈を使用して、プロパティが必要であることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="8823f-111">You can use Data Annotations to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="8823f-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8823f-112">Fluent API</span></span>

<span data-ttu-id="8823f-113">プロパティが必要であることを示す、Fluent API を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="8823f-113">You can use the Fluent API to indicate that a property is required.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

