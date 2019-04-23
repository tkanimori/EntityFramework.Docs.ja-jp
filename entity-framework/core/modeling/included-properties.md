---
title: 含める/除外するプロパティ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 022534091bb48e491c8808791a401216a339d7b0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929825"
---
# <a name="including--excluding-properties"></a><span data-ttu-id="0de56-102">含める/除外するプロパティ</span><span class="sxs-lookup"><span data-stu-id="0de56-102">Including & Excluding Properties</span></span>

<span data-ttu-id="0de56-103">読み取りし、書き込み値をデータベースから/にしようし、モデル内のプロパティを含む EF がそのプロパティに関するメタデータを持っていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="0de56-103">Including a property in the model means that EF has metadata about that property and will attempt to read and write values from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="0de56-104">規約</span><span class="sxs-lookup"><span data-stu-id="0de56-104">Conventions</span></span>

<span data-ttu-id="0de56-105">慣例により、getter および setter を含むパブリック プロパティは、モデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="0de56-105">By convention, public properties with a getter and a setter will be included in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="0de56-106">データの注釈</span><span class="sxs-lookup"><span data-stu-id="0de56-106">Data Annotations</span></span>

<span data-ttu-id="0de56-107">データ注釈を使用して、モデルからプロパティを除外することができます。</span><span class="sxs-lookup"><span data-stu-id="0de56-107">You can use Data Annotations to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a><span data-ttu-id="0de56-108">Fluent API</span><span class="sxs-lookup"><span data-stu-id="0de56-108">Fluent API</span></span>

<span data-ttu-id="0de56-109">Fluent API を使用して、モデルからプロパティを除外することができます。</span><span class="sxs-lookup"><span data-stu-id="0de56-109">You can use the Fluent API to exclude a property from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
