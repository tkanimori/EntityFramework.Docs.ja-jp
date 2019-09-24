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
# <a name="including--excluding-properties"></a>プロパティを含める/除外する

モデルにプロパティを含めると、EF にはそのプロパティに関するメタデータが含まれ、データベースとの間で値の読み取りおよび書き込みが試行されます。

## <a name="conventions"></a>規約

慣例により、getter と setter を持つパブリックプロパティがモデルに含まれます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、モデルからプロパティを除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデルからプロパティを除外することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?highlight=12,13)]
