---
title: 関連データの読み込み - EF Core
description: Entity Framework Core で関連データを読み込むためのさまざまな方法
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078851"
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。 関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。

* **[一括読み込み](xref:core/querying/related-data/eager)** 。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。
* **[明示的読み込み](xref:core/querying/related-data/explicit)** 。後でデータベースから明示的に関連データが読み込まれることを意味します。
* **[遅延読み込み](xref:core/querying/related-data/lazy)** 。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。

> [!TIP]
> GitHub 上でこのセクションの下から[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)を参照できます。
