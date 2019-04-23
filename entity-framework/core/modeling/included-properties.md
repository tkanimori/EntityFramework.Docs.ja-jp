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
# <a name="including--excluding-properties"></a>含める/除外するプロパティ

読み取りし、書き込み値をデータベースから/にしようし、モデル内のプロパティを含む EF がそのプロパティに関するメタデータを持っていることを意味します。

## <a name="conventions"></a>規約

慣例により、getter および setter を含むパブリック プロパティは、モデルに含まれます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、モデルからプロパティを除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデルからプロパティを除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
