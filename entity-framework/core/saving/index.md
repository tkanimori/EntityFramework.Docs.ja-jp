---
title: データの保存 - EF Core
description: Entity Framework Core を使用したデータ保存の概要
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072474"
---
# <a name="saving-data"></a>データの保存

各コンテキスト インスタンスには、データベースに書き込む必要がある変更の記録を担う `ChangeTracker` があります。 エンティティ クラスのインスタンスを変更すると、変更内容が `ChangeTracker` で記録され、`SaveChanges` を呼び出したときにデータベースに書き込まれます。 このデータベース プロバイダーは、変更をデータベース固有の操作 (リレーショナル データベースの `INSERT`、`UPDATE`、`DELETE` コマンドなど) に変換する作業を担当します。
