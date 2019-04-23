---
title: 列のマッピング - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929863"
---
# <a name="column-mapping"></a>列マッピング

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

列マッピングは、列データをからクエリを実行し、データベース内に保存する必要がありますを識別します。

## <a name="conventions"></a>規約

慣例により、プロパティと同じ名前の列にマップする各プロパティ設定されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティがマップされている列を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティがマップされている列を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
