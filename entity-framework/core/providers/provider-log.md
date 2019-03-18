---
title: プロバイダーに影響を与える変更 - EF Core のログ
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: 70fe2d934901f5366c96904b08f49a35f6590b47
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2019
ms.locfileid: "58131404"
---
# <a name="provider-impacting-changes"></a>プロバイダーに影響を与える変更

このページには、プル要求に対応するためには、他のデータベース プロバイダーの作成者が必要な EF Core リポジトリへのリンクが含まれています。 目的がプロバイダーを新しいバージョンに更新するときに、既存のサードパーティ データベース プロバイダーの作成者の開始点を提供することです。

2.1 から 2.2 への変更でこのログを開始します。 使用して 2.1 より前、 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)問題とプル要求のラベル。

## <a name="22-----30"></a>2.2 ---> 3.0

* https://github.com/aspnet/EntityFrameworkCore/pull/14022
  * 廃止された Api が削除された、折りたたまれた省略可能なパラメーター オーバー ロード
  * 削除された DatabaseColumn.GetUnderlyingStoreType()
* https://github.com/aspnet/EntityFrameworkCore/pull/14589
  * 廃止された Api の削除
* https://github.com/aspnet/EntityFrameworkCore/pull/15044
  * CharTypeMapping のサブクラスは基本の実装でいくつかのバグを修正するために必要な動作の変更により切断されている可能性があります。

## <a name="21-----22"></a>2.1 ---> 2.2

### <a name="test-only-changes"></a>テスト専用の変更

* [https://github.com/aspnet/EntityFrameworkCore/pull/12057](https://github.com/aspnet/EntityFrameworkCore/pull/12057) -テストでカスタマイズ可能な SQL の区切り文字を許可します。
  * 非厳格フローティングできるようにするテストの変更ではポイントイン BuiltInDataTypesTestBase の比較
  * 別の SQL の区切り文字を再利用するクエリのテストを実行できるテストの変更
* [https://github.com/aspnet/EntityFrameworkCore/pull/12072](https://github.com/aspnet/EntityFrameworkCore/pull/12072) -リレーショナル仕様テストに DbFunction テストを追加します。
  * これらのテストは、すべてのデータベース プロバイダーに対して実行できるように
* [https://github.com/aspnet/EntityFrameworkCore/pull/12362](https://github.com/aspnet/EntityFrameworkCore/pull/12362) でテストは、非同期のクリーンアップ
  * 削除`Wait`呼び出し、非同期、不要ないくつかのテスト メソッドの名前を変更
* [https://github.com/aspnet/EntityFrameworkCore/pull/12666](https://github.com/aspnet/EntityFrameworkCore/pull/12666) -ログ記録テスト インフラストラクチャを統合します。
  * 追加`CreateListLoggerFactory`対応するため、これらのテストを使用して、プロバイダーが必要になりますいくつかの以前のログ記録インフラストラクチャの削除
* [https://github.com/aspnet/EntityFrameworkCore/pull/12500](https://github.com/aspnet/EntityFrameworkCore/pull/12500) -同期的および非同期的に複数のクエリのテストを実行します。
  * テスト名およびファクタリングが変更された、対応するため、これらのテストを使用して、プロバイダーが必要になります
* [https://github.com/aspnet/EntityFrameworkCore/pull/12766](https://github.com/aspnet/EntityFrameworkCore/pull/12766) 名前変更、ComplexNavigations モデルでのナビゲーション
  * これらのテストを使用して、プロバイダーは、対応する必要があります。
* [https://github.com/aspnet/EntityFrameworkCore/pull/12141](https://github.com/aspnet/EntityFrameworkCore/pull/12141) -機能テストの破棄ではなく、プールにコンテキストを返します
  * この変更には、対応するためのプロバイダーを必要がありますテスト リファクタリングが含まれています。


### <a name="test-and-product-code-changes"></a>テストと製品コードの変更

* [https://github.com/aspnet/EntityFrameworkCore/pull/12109](https://github.com/aspnet/EntityFrameworkCore/pull/12109) -統合 RelationalTypeMapping.Clone メソッド
  * 派生クラスでの単純化するため、RelationalTypeMapping 2.1 での変更を許可します。 私たちを信じて、プロバイダーの重大ながこれが、プロバイダーを利用してこの変更の派生型のマッピング クラス。
* [https://github.com/aspnet/EntityFrameworkCore/pull/12069](https://github.com/aspnet/EntityFrameworkCore/pull/12069) -タグが付けられたまたは名前付きクエリ
  * LINQ クエリをタグ付けし、それらのタグを SQL 内のコメントとして表示するためのインフラストラクチャを追加します。 SQL の生成に対応するためのプロバイダーが必要です。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13115](https://github.com/aspnet/EntityFrameworkCore/pull/13115) -NTS を使用して空間データをサポートします。
  * 型のマッピングとメンバー翻訳外部プロバイダーを登録するのには、します。
    * プロバイダーは、基本で呼び出す必要があります。動作するためには、その ITypeMappingSource 実装 FindMapping()
  * プロバイダー間で一貫性のあるプロバイダーに空間的なサポートを追加するには、このパターンに従います。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13199](https://github.com/aspnet/EntityFrameworkCore/pull/13199) -サービス プロバイダーの作成のデバッグ機能の強化を追加します。
  * により、内部のサービス プロバイダーを再構築中理由を理解するのに役立つ新しいインターフェイスを実装する DbContextOptionsExtensions
* [https://github.com/aspnet/EntityFrameworkCore/pull/13289](https://github.com/aspnet/EntityFrameworkCore/pull/13289) -により CanConnect API 使用するための正常性チェック
  * このプル要求の追加の概念`CanConnect`正常性の ASP.NET Core で使用されるデータベースが使用可能なかどうかを判断するを確認します。 リレーショナルの実装では、既定では、呼び出すだけ`Exist`がプロバイダーに応じて実装別のものにできます。 非リレーショナルのプロバイダーは、使用するのには、正常性チェックのために、新しい API を実装する必要があります。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13306](https://github.com/aspnet/EntityFrameworkCore/pull/13306) -DbParameter のサイズを設定しないように基本 RelationalTypeMapping を更新します。
  * 切り捨てが発生する可能性があるため既定サイズの設定を停止します。 プロバイダーは、サイズを設定する必要がある場合に、独自のロジックを追加する必要があります。
* https://github.com/aspnet/EntityFrameworkCore/pull/13372 -RevEng:常に 10 進数の列の列の種類を指定します。
  * 常に規則を構成するのではなく、スキャフォールディングされたコードの 10 進数の列の列の種類を構成します。
  * プロバイダーは、エンドユーザー側で変更する必要はありません。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13469](https://github.com/aspnet/EntityFrameworkCore/pull/13469) -SQL の CASE 式を生成するため CaseExpression を追加します。
* [https://github.com/aspnet/EntityFrameworkCore/pull/13648](https://github.com/aspnet/EntityFrameworkCore/pull/13648) -引数の結果ストア型の推定を向上させるために SqlFunctionExpression で型のマッピングを指定する機能を追加します。
