---
title: 列マッピング-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: eaffc0cc1642f64edabeeef974f5f6de7a23b656
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197205"
---
# <a name="column-mapping"></a>列マッピング

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

列マッピングでは、データベースのどの列データに対してクエリを行い、保存するかを指定します。

## <a name="conventions"></a>規約

慣例により、各プロパティは、プロパティと同じ名前の列にマップされるように設定されます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティがマップされる列を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティがマップされる列を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Column.cs?highlight=11-13)]
