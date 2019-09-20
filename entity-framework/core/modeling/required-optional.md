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
# <a name="required-and-optional-properties"></a>必須および省略可能なプロパティ

プロパティが有効`null`である場合、プロパティは省略可能と見なされます。 が`null`プロパティに割り当てられる有効な値でない場合、必須プロパティと見なされます。

## <a name="conventions"></a>規約

慣例により、.net 型に null を含めることができるプロパティは、オプション`string`( `int?` `byte[]`、、など) として構成されます。 CLR 型に null を含めることができないプロパティは、`int`必要`decimal`に`bool`応じて構成されます (、、など)。

> [!NOTE]  
> .NET 型に null を含めることができないプロパティをオプションとして構成することはできません。 プロパティは、常に Entity Framework によって要求されると見なされます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティが必要であることを示すことができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティが必要であることを示すことができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

