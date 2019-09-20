---
title: データ型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: d667cbcb821e321faed36d097b531c7c55b81248
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149169"
---
# <a name="data-types"></a>データの種類

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

データ型は、プロパティがマップされている列のデータベース固有の型を参照します。

## <a name="conventions"></a>規約

慣例により、データベースプロバイダーは、プロパティの .NET 型に基づいてデータ型を選択します。 また、構成された[最大長](../max-length.md)、プロパティが主キーの一部であるかどうかなど、他のメタデータも考慮する必要があります。

たとえば、SQL Server は、 `datetime2(7)`プロパティの場合は、プロパティの場合は`string` 、キーとして使用されるプロパティの場合は`nvarchar(450)`を使用`DateTime` `nvarchar(max)` `string`します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、列のデータ型を正確に指定できます。

たとえば、次のコードは`Url` 、の最大`200`長`Rating`を持つ非 unicode 文字列としてを構成し、 `5`の有効桁数`2`と小数点以下桁数を指定します。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

また、Fluent API を使用して、列に同じデータ型を指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
