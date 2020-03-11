---
title: プロバイダーに影響する変更のログ-EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: b911a2da493e20c4e4ce6f1e25024bd0efd38b44
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414771"
---
# <a name="provider-impacting-changes"></a>プロバイダーに影響を与える変更

このページには、EF Core リポジトリに対するプル要求へのリンクが含まれており、他のデータベースプロバイダーの作成者が対処する必要がある場合があります。 その目的は、プロバイダーを新しいバージョンに更新する際に、既存のサードパーティのデータベースプロバイダーの作成者のための出発点となることです。

2\.1 から2.2 への変更で、このログを開始します。 2\.1 より前では、 [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)を使用して、問題とプル要求にラベルを[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)していました。

## <a name="22-----30"></a>2.2---> 3.0

[アプリケーションレベルの重大な変更](../what-is-new/ef-core-3.0/breaking-changes.md)の多くは、プロバイダーにも影響します。

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * 廃止された Api と折りたたまれた省略可能なパラメーターのオーバーロードを削除しました
  * GetUnderlyingStoreType () を削除しました
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * 廃止された Api を削除しました
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * CharTypeMapping のサブクラスは、基本実装でいくつかのバグを修正するために必要な動作の変更により、破損している可能性があります。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * IDatabaseModelFactory の基底クラスを追加し、パラメーターオブジェクトを使用して今後の中断を軽減するように更新しました。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * MigrationsSqlGenerator のパラメーターオブジェクトを使用して、将来の中断を軽減します。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * ログレベルを明示的に構成するには、プロバイダーが使用している Api にいくつかの変更を加える必要があります。 具体的には、プロバイダーがログインフラストラクチャを直接使用している場合、この変更によってその使用が中断される可能性があります。 また、今後のインフラストラクチャを使用するプロバイダーは、`LoggingDefinitions` または `RelationalLoggingDefinitions`から派生する必要があります。 例については、SQL Server とメモリ内のプロバイダーを参照してください。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * コア、リレーショナル、抽象化の各リソース文字列がパブリックになりました。
  * `CoreLoggerExtensions` と `RelationalLoggerExtensions` がパブリックになりました。 プロバイダーは、コアレベルまたはリレーショナルレベルで定義されているイベントをログに記録するときに、これらの Api を使用する必要があります。 ログ記録リソースに直接アクセスしないでください。これらは依然として内部です。
  * `IRawSqlCommandBuilder` がシングルトンサービスからスコープ付きサービスに変更されました
  * `IMigrationsSqlGenerator` がシングルトンサービスからスコープ付きサービスに変更されました
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * リレーショナルコマンドを構築するためのインフラストラクチャは公開されているため、プロバイダーが安全に使用し、若干のリファクタリングを行うことができます。
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` がスコープサービスから一時的なサービスに変更されました
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` がスコープサービスからシングルトンサービスに変更されました
  * また、`ISingletonUpdateSqlGenerator` が削除されました。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * プロバイダーによって使用されていた多数の内部コードが公開されるようになりました。
  * Necssary を公開する場所から除外されているため、`IndentedStringBuilder` 参照する必要はなくなりました。
  * `NonCapturingLazyInitializer` の使用は、BCL の `LazyInitializer` に置き換える必要があります
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * この変更については、「アプリケーションの互換性に影響する変更点」ドキュメントで詳しく説明されています。 プロバイダーによっては、EF core のテストによってこの問題が発生することがよくあります。そのため、テストインフラストラクチャが変更され、可能性が低くなります。
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` が簡略化されました
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * StartsWith の変換が、プロバイダーが必要とする方法で変更されました。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * 規則セットサービスが変更されました。 プロバイダーは、"ProviderConventionSet" または "RelationalConventionSet" から継承するようになりました。
  * カスタマイズは `IConventionSetCustomizer` services を使用して追加できますが、これはプロバイダーではなく、他の拡張機能によって使用されることを意図しています。
  * 実行時に使用される規則は、`IConventionSetBuilder`から解決する必要があります。
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * 内部型を使用する必要がないように、データシード処理はパブリック API にリファクタリングされています。 シード処理は、すべてのリレーショナルプロバイダーの基本リレーショナルクラスによって処理されるため、非リレーショナルプロバイダーにのみ影響します。

## <a name="21-----22"></a>2.1---> 2.2

### <a name="test-only-changes"></a>テスト専用の変更

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057>-テストでのカスタマイズ可能な SQL の使用を許可する
  * BuiltInDataTypesTestBase で非厳密な浮動小数点比較を可能にするテストの変更
  * 異なる SQL 区切りでクエリテストを再利用できるようにする変更をテストする
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072>-DbFunction テストをリレーショナル仕様テストに追加する
  * このようなテストは、すべてのデータベースプロバイダーに対して実行できます。
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362>-非同期テストのクリーンアップ
  * `Wait` 呼び出し、不要な非同期の削除、および一部のテストメソッドの名前変更
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666>-ログ記録テストインフラストラクチャの統合
  * `CreateListLoggerFactory` が追加され、以前のログ記録インフラストラクチャが削除されました。そのためには、これらのテストを使用するプロバイダーが必要です。
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500>-同期と非同期の両方で、より多くのクエリテストを実行する
  * テスト名とファクタリングが変更されました。これらのテストを使用して応答するには、プロバイダーが必要です
* <https://github.com/aspnet/EntityFrameworkCore/pull/12766>-ComplexNavigations モデルでナビゲーションの名前を変更する
  * これらのテストを使用するプロバイダーは、
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141>-機能テストで破棄するのではなく、コンテキストをプールに返します
  * この変更には、プロバイダーに対応するために必要なテストリファクタリングが含まれています。

### <a name="test-and-product-code-changes"></a>テストと製品コードの変更

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109>-RelationalTypeMapping メソッドを統合する
  * 2\.1 の変更は、派生クラスの単純化に使用できる RelationalTypeMapping に変更されました。 これがプロバイダーによって中断されているとは限りませんが、派生型のマッピングクラスでは、プロバイダーがこの変更を利用できます。
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069>-タグ付きまたは名前付きクエリ
  * LINQ クエリにタグを付け、それらのタグを SQL にコメントとして表示するためのインフラストラクチャを追加します。 これには、プロバイダーが SQL 生成時に応答する必要がある場合があります。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115>-NTS を使用した空間データのサポート
  * 型マッピングとメンバートランスレーターをプロバイダーの外部に登録できるようにします
    * プロバイダーは base を呼び出す必要があります。ITypeMappingSource 実装で FindMapping () を使用するための実装
  * プロバイダー間で一貫した空間サポートをプロバイダーに追加するには、このパターンに従います。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199>-サービスプロバイダーの作成用に強化されたデバッグ機能を追加する
  * 内部サービスプロバイダーが再構築されている理由を理解するのに役立つ新しいインターフェイスを DbContextOptionsExtensions が実装できるようにします。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289>-正常性チェックで使用する CanConnect API を追加します
  * この PR は、データベースが使用可能かどうかを判断するために ASP.NET Core 正常性チェックによって使用される `CanConnect` の概念を追加します。 既定では、リレーショナル実装は `Exist`のみを呼び出しますが、プロバイダーは必要に応じて別のものを実装できます。 非リレーショナルプロバイダーは、正常性チェックを使用できるようにするために、新しい API を実装する必要があります。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306>-DbParameter サイズを設定しないようにベース RelationalTypeMapping を更新する
  * 既定では、切り捨てが発生する可能性があるため、サイズの設定を停止します。 サイズを設定する必要がある場合、プロバイダーは独自のロジックを追加する必要があります。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372>-RevEng: 常に decimal 型の列に対して列の型を指定する
  * スキャフォールディングコードでは、規則によって構成するのではなく、常に decimal 型の列に対して列の型を構成します。
  * プロバイダーの最後に変更を加える必要はありません。
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469>-SQL ケース式を生成するための CaseExpression を追加します
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648>-引数と結果のストア型の推定を向上させるために、SqlFunctionExpression で型のマッピングを指定する機能を追加します。
