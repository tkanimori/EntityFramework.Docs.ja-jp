---
title: 計算列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 4e92ed6d785f3b7bdf54015101bdcddb9e4e0e1c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655916"
---
# <a name="computed-columns"></a>計算列

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

計算列とは、データベース内で値が計算される列のことです。 計算列では、テーブル内の他の列を使用してその値を計算できます。

## <a name="conventions"></a>規約

慣例により、計算列はモデルに作成されません。

## <a name="data-annotations"></a>データの注釈

計算列は、データ注釈を使用して構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティを計算列にマップする必要があることを指定できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ComputedColumn.cs?name=ComputedColumn&highlight=9)]
