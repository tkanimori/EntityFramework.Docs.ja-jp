---
title: プロパティを除外 & を含める-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: cd111af891ef0bbaccf515eed0c1991f105bd362
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197415"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="03347-102">プロパティを含める/除外する</span><span class="sxs-lookup"><span data-stu-id="03347-102">Including & Excluding Properties</span></span>

<span data-ttu-id="03347-103">モデルにプロパティを含めると、EF にはそのプロパティに関するメタデータが含まれ、データベースとの間で値の読み取りおよび書き込みが試行されます。</span><span class="sxs-lookup"><span data-stu-id="03347-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="03347-104">規約</span><span class="sxs-lookup"><span data-stu-id="03347-104">Conventions</span></span>

<span data-ttu-id="03347-105">慣例により、getter と setter を持つパブリックプロパティがモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="03347-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="03347-106">データの注釈</span><span class="sxs-lookup"><span data-stu-id="03347-106">Data Annotations</span></span>

<span data-ttu-id="03347-107">データ注釈を使用して、モデルからプロパティを除外することができます。</span><span class="sxs-lookup"><span data-stu-id="03347-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="03347-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="03347-108">Fluent API</span></span>

<span data-ttu-id="03347-109">Fluent API を使用して、モデルからプロパティを除外することができます。</span><span class="sxs-lookup"><span data-stu-id="03347-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?highlight=12,13)]
