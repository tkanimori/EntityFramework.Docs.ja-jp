---
title: プロバイダーに影響を与える変更 - EF Core のログ
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: ee73940e3c0030b76e73438b1852cc29ebeadb45
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998361"
---
# <a name="provider-impacting-changes"></a>プロバイダーに影響を与える変更

このページには、プル要求に対応するためには、他のデータベース プロバイダーの作成者が必要な EF Core リポジトリへのリンクが含まれています。 目的がプロバイダーを新しいバージョンに更新するときに、既存のサードパーティ データベース プロバイダーの作成者の開始点を提供することです。

2.1 から 2.2 への変更でこのログを開始します。 使用して 2.1 より前、 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)問題とプル要求のラベル。

### <a name="21-----22"></a>2.1 2.2---> します。

#### <a name="test-only-changes"></a>テスト専用の変更

* https://github.com/aspnet/EntityFrameworkCore/pull/12057 -テストでカスタマイズ可能な SQL の区切り文字を許可します。
  * 非厳格フローティングできるようにするテストの変更ではポイントイン BuiltInDataTypesTestBase の比較
  * 別の SQL の区切り文字を再利用するクエリのテストを実行できるテストの変更
* https://github.com/aspnet/EntityFrameworkCore/pull/12072 -リレーショナル仕様テストに DbFunction テストを追加します。
  * これらのテストは、すべてのデータベース プロバイダーに対して実行できるように
* https://github.com/aspnet/EntityFrameworkCore/pull/12362 でテストは、非同期のクリーンアップ
  * 削除`Wait`呼び出し、非同期、不要ないくつかのテスト メソッドの名前を変更
* https://github.com/aspnet/EntityFrameworkCore/pull/12666 -ログ記録テスト インフラストラクチャを統合します。
  * 追加`CreateListLoggerFactory`対応するため、これらのテストを使用して、プロバイダーが必要になりますいくつかの以前のログ記録インフラストラクチャの削除
* https://github.com/aspnet/EntityFrameworkCore/pull/12500 -同期的および非同期的に複数のクエリのテストを実行します。
  * テスト名およびファクタリングが変更された、対応するため、これらのテストを使用して、プロバイダーが必要になります
* https://github.com/aspnet/EntityFrameworkCore/pull/12766 名前変更、ComplexNavigations モデルでのナビゲーション
  * これらのテストを使用して、プロバイダーは、対応する必要があります。
* https://github.com/aspnet/EntityFrameworkCore/pull/12141 -機能テストの破棄ではなく、プールにコンテキストを返します
  * この変更には、対応するためのプロバイダーを必要がありますテスト リファクタリングが含まれています。


#### <a name="test-and-product-code-changes"></a>テストと製品コードの変更

* https://github.com/aspnet/EntityFrameworkCore/pull/12109 -統合 RelationalTypeMapping.Clone メソッド
  * 派生クラスでの単純化するため、RelationalTypeMapping 2.1 での変更を許可します。 私たちを信じて、プロバイダーの重大ながこれが、プロバイダーを利用してこの変更の派生型のマッピング クラス。
* https://github.com/aspnet/EntityFrameworkCore/pull/12069 -タグが付けられたまたは名前付きクエリ
  * LINQ クエリをタグ付けし、それらのタグを SQL 内のコメントとして表示するためのインフラストラクチャを追加します。 SQL の生成に対応するためのプロバイダーが必要です。
