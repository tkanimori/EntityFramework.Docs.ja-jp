---
title: データ型 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 9060f66c752be01090ce40be6bf3a32f348ce571
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993522"
---
# <a name="data-types"></a>データの種類

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

データ型は、データベースの特定の種類のプロパティがマップされている列を指します。

## <a name="conventions"></a>規約

慣例により、データベース プロバイダーは、プロパティの CLR 型に基づいたデータ型を選択します。 アカウントにもかかるなど、構成されたその他のメタデータ[最大長](../max-length.md)プロパティが主キーなどの一部であるかどうか。

たとえば、SQL Server を使用して`datetime2(7)`の`DateTime`プロパティ、および`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`の`string`キーとして使用されるプロパティ)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、列の正確なデータの種類を指定できます。

たとえば、次のコードでは構成`Url`の最大長の unicode 以外の文字列として`200`と`Rating`で有効桁数の 10 進数として`5`のスケールと`2`します。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

列の同じデータ型を指定するのに、Fluent API を使用することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
