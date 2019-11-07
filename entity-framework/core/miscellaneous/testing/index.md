---
title: EF Core を使ったコンポーネントのテスト - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 1ca900528ed42ad4b41016f22964c3494b0286eb
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655783"
---
# <a name="testing-components-using-ef-core"></a>EF Core を使ったコンポーネントのテスト

本物のデータベースへの接続をシミュレーションすることでコンポーネントをテストすることがあります。実際にデータベース I/O を操作してオーバーヘッドを発生させることを回避するためです。

これには、次の 2 つの選択肢があります。

* [SQLite メモリ内モード](sqlite.md)では、リレーショナル データベースのように振る舞うプロバイダーに対して効率的なテストを記述できます。
* [InMemory プロバイダー](in-memory.md)は依存性が最小限に抑えられた軽量のプロバイダーですが、常にリレーショナル データベースのように振る舞うとは限りません。
