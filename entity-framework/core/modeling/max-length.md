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
# <a name="maximum-length"></a>最大長

最大長を構成すると、特定のプロパティに対して使用する適切なデータ型に関するヒントがデータストアに提供されます。 最大長は、配列のデータ型 ( `string`や`byte[]`など) にのみ適用されます。

> [!NOTE]  
> Entity Framework は、データをプロバイダーに渡す前に、最大長の検証を行いません。 必要に応じて、プロバイダーまたはデータストアが検証する必要があります。 たとえば、SQL Server を対象とする場合、基になる列のデータ型では過剰なデータを格納できないため、最大長を超えると例外が発生します。

## <a name="conventions"></a>規約

慣例により、プロパティに適切なデータ型を選択するために、データベースプロバイダーが使用されます。 長さがのプロパティの場合、データベースプロバイダーは通常、データの長さが最も長いデータ型を選択します。 たとえば、Microsoft SQL Server はプロパティに`nvarchar(max)` `string`を使用します`nvarchar(450)` (または、列がキーとして使用されている場合)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティの最大長を構成できます。 この例では、この SQL Server を対象とする`nvarchar(500)`と、データ型が使用されます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティの最大長を構成できます。 この例では、この SQL Server を対象とする`nvarchar(500)`と、データ型が使用されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
