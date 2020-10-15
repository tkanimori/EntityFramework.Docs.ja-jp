---
title: 関連データの読み込み - EF Core
description: Entity Framework Core で関連データを読み込むためのさまざまな方法
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063674"
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。 関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。

* **[一括読み込み](xref:core/querying/related-data/eager)** 。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。
* **[明示的読み込み](xref:core/querying/related-data/explicit)** 。後でデータベースから明示的に関連データが読み込まれることを意味します。
* **[遅延読み込み](xref:core/querying/related-data/lazy)** 。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。

> [!TIP]
> GitHub 上でこのセクションの下から[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData)を参照できます。
