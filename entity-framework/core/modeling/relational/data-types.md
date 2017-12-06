---
title: "データ型の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
ms.technology: entity-framework-core
uid: core/modeling/relational/data-types
ms.openlocfilehash: fd4668a3f9554eb9d3b1161d5dddce2fcdcac712
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="data-types"></a>データ型

> [!NOTE]  
> このセクションの構成は、一般にリレーショナル データベースに適用されます。 ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。

データ型は、データベースの特定の種類のプロパティがマップされている列を参照します。

## <a name="conventions"></a>規則

慣例により、データベース プロバイダーは、プロパティの CLR 型に基づいたデータ型を選択します。 アカウントにもかかるなど、構成されたその他のメタデータ[最大長](../max-length.md)プロパティが主キーなどの一部であるかどうか、します。

たとえば、SQL Server を使用して`datetime2(7)`の`DateTime`プロパティ、および`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`の`string`キーとして使用されるプロパティ)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、正確なデータ型の列を指定することができます。

たとえば、次のコード構成`Url`の最大長を持つ非 unicode 文字列として`200`と`Rating`の有効桁数と小数点`5`の拡大/縮小`2`です。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、同じデータ型の列を指定することができますも。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
