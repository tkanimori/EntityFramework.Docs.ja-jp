---
title: データの保存 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: c610ea2a9138482f93d2d54c9085ef827af276c8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998181"
---
# <a name="saving-data"></a>データの保存

各コンテキスト インスタンスには、データベースに書き込む必要がある変更の記録を担う `ChangeTracker` があります。 エンティティ クラスのインスタンスを変更すると、変更内容が `ChangeTracker` で記録され、`SaveChanges` を呼び出したときにデータベースに書き込まれます。 このデータベース プロバイダーは、変更をデータベース固有の操作 (リレーショナル データベースの `INSERT`、`UPDATE`、`DELETE` コマンドなど) に変換する作業を担当します。
