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
# <a name="required-and-optional-properties"></a>必須およびオプションのプロパティ

プロパティを含めるには有効な場合は省略可能なと見なされます`null`します。 場合`null`し、必要なプロパティであると見なされるプロパティに割り当てられる有効な値ではありません。

## <a name="conventions"></a>規約

慣例により、プロパティの CLR 型が null を含めることができますが構成するオプションとして (`string`、 `int?`、`byte[]`など。)。 CLR 型が null を含めることはできませんのプロパティを構成は必要に応じて (`int`、 `decimal`、`bool`など。)。

> [!NOTE]  
> CLR 型を null に含めることはできませんプロパティは、省略可能として構成できません。 常に Entity Framework によって必要なプロパティが考慮されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティが必要であることを示すことができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

プロパティが必要であることを示す、Fluent API を使用することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

