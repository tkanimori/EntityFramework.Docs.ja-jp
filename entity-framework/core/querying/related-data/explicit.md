---
title: 関連データの明示的読み込み - EF Core
description: Entity Framework Core による関連データの明示的読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: def1c8c2736c64bc6f39af0d344fd1330f8a06f5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210455"
---
# <a name="explicit-loading-of-related-data"></a>関連データの明示的読み込み

## <a name="explicit-loading"></a>明示的読み込み

`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。 変更の追跡が有効な場合、クエリによってエンティティが具現化されると、EF Core では、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティが自動的に設定されます。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティが自動的に設定されます。

## <a name="querying-related-entities"></a>関連エンティティのクエリ

また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。

これにより、クエリに追加の演算子を適用できます。 たとえば、関連エンティティをメモリに読み込まずに集計演算子を適用できます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
