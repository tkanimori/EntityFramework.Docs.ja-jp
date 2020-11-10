---
title: プロバイダーに影響する変更のログ-EF Core
description: プロバイダーに影響を与える Entity Framework Core の変更のログ
author: ajcvickers
ms.date: 08/08/2018
uid: core/providers/provider-log
ms.openlocfilehash: 8ba2c738c87ea918e5f2c10f4446a7043c97a501
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430158"
---
# <a name="provider-impacting-changes"></a>プロバイダーに影響を与える変更

このページには、EF Core リポジトリに対するプル要求へのリンクが含まれており、他のデータベースプロバイダーの作成者が対処する必要がある場合があります。 その目的は、プロバイダーを新しいバージョンに更新する際に、既存のサードパーティのデータベースプロバイダーの作成者のための出発点となることです。

2.1 から2.2 への変更で、このログを開始します。 2.1 より前の [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) 問題とプル要求では、ラベルとラベルを使用していました。

## <a name="22-----3x"></a>2.2---> 3.x

[アプリケーションレベルの重大な変更](xref:core/what-is-new/ef-core-3.x/breaking-changes)の多くは、プロバイダーにも影響します。

* <https://github.com/dotnet/efcore/pull/14022>
  * 廃止された Api と折りたたまれた省略可能なパラメーターのオーバーロードを削除しました
  * GetUnderlyingStoreType () を削除しました
* <https://github.com/dotnet/efcore/pull/14589>
  * 廃止された Api を削除しました
* <https://github.com/dotnet/efcore/pull/15044>
  * CharTypeMapping のサブクラスは、基本実装でいくつかのバグを修正するために必要な動作の変更により、破損している可能性があります。
* <https://github.com/dotnet/efcore/pull/15090>
  * IDatabaseModelFactory の基底クラスを追加し、パラメーターオブジェクトを使用して今後の中断を軽減するように更新しました。
* <https://github.com/dotnet/efcore/pull/15123>
  * MigrationsSqlGenerator のパラメーターオブジェクトを使用して、将来の中断を軽減します。
* <https://github.com/dotnet/efcore/pull/14972>
  * ログレベルを明示的に構成するには、プロバイダーが使用している Api にいくつかの変更を加える必要があります。 具体的には、プロバイダーがログインフラストラクチャを直接使用している場合、この変更によってその使用が中断される可能性があります。 また、今後のインフラストラクチャを使用するプロバイダーは、またはから派生する必要があり `LoggingDefinitions` `RelationalLoggingDefinitions` ます。 例については、SQL Server とメモリ内のプロバイダーを参照してください。
* <https://github.com/dotnet/efcore/pull/15091>
  * コア、リレーショナル、抽象化の各リソース文字列がパブリックになりました。
  * `CoreLoggerExtensions` と `RelationalLoggerExtensions` がパブリックになりました。 プロバイダーは、コアレベルまたはリレーショナルレベルで定義されているイベントをログに記録するときに、これらの Api を使用する必要があります。 ログ記録リソースに直接アクセスしないでください。これらは依然として内部です。
  * `IRawSqlCommandBuilder` シングルトンサービスからスコープ付きサービスに変更されました
  * `IMigrationsSqlGenerator` シングルトンサービスからスコープ付きサービスに変更されました
* <https://github.com/dotnet/efcore/pull/14706>
  * リレーショナルコマンドを構築するためのインフラストラクチャは公開されているため、プロバイダーが安全に使用し、若干のリファクタリングを行うことができます。
* <https://github.com/dotnet/efcore/pull/14733>
  * `ILazyLoader` スコープサービスから一時的なサービスに変更されました
* <https://github.com/dotnet/efcore/pull/14610>
  * `IUpdateSqlGenerator` スコープ付きサービスからシングルトンサービスに変更されました
  * また、 `ISingletonUpdateSqlGenerator` が削除されました。
* <https://github.com/dotnet/efcore/pull/15067>
  * プロバイダーによって使用されていた多数の内部コードが公開されるようになりました。
  * `IndentedStringBuilder`公開された場所からは除外されているため、necssary を参照する必要はなくなります。
  * の使用は `NonCapturingLazyInitializer` `LazyInitializer` 、BCL からで置き換える必要があります。
* <https://github.com/dotnet/efcore/pull/14608>
  * この変更については、「アプリケーションの互換性に影響する変更点」ドキュメントで詳しく説明されています。 プロバイダーによっては、EF core のテストによってこの問題が発生することがよくあります。そのため、テストインフラストラクチャが変更され、可能性が低くなります。
* <https://github.com/dotnet/efcore/issues/13961>
  * `EntityMaterializerSource` 簡略化されました
* <https://github.com/dotnet/efcore/pull/14895>
  * StartsWith の変換が、プロバイダーが必要とする方法で変更されました。
* <https://github.com/dotnet/efcore/pull/15168>
  * 規則セットサービスが変更されました。 プロバイダーは、"ProviderConventionSet" または "RelationalConventionSet" から継承するようになりました。
  * カスタマイズはサービスを通じて追加でき `IConventionSetCustomizer` ますが、これはプロバイダーではなく、他の拡張機能によって使用されることを意図しています。
  * 実行時に使用される規約は、から解決する必要があり `IConventionSetBuilder` ます。
* <https://github.com/dotnet/efcore/pull/15288>
  * 内部型を使用する必要がないように、データシード処理はパブリック API にリファクタリングされています。 シード処理は、すべてのリレーショナルプロバイダーの基本リレーショナルクラスによって処理されるため、非リレーショナルプロバイダーにのみ影響します。

## <a name="21-----22"></a>2.1---> 2.2

### <a name="test-only-changes"></a>テスト専用の変更

* <https://github.com/dotnet/efcore/pull/12057> -テストでカスタマイズ可能な SQL を使用できるようにします
  * BuiltInDataTypesTestBase で非厳密な浮動小数点比較を可能にするテストの変更
  * 異なる SQL 区切りでクエリテストを再利用できるようにする変更をテストする
* <https://github.com/dotnet/efcore/pull/12072> -DbFunction テストをリレーショナル仕様テストに追加する
  * このようなテストは、すべてのデータベースプロバイダーに対して実行できます。
* <https://github.com/dotnet/efcore/pull/12362> -非同期テストのクリーンアップ
  * `Wait`呼び出し、不要な非同期を削除し、いくつかのテストメソッドの名前を変更しました
* <https://github.com/dotnet/efcore/pull/12666> -ログテストインフラストラクチャの統合
  * `CreateListLoggerFactory`以前のログ記録インフラストラクチャを追加および削除しました。このインフラストラクチャでは、これらのテストを使用して応答するプロバイダーが必要になります。
* <https://github.com/dotnet/efcore/pull/12500> -同期と非同期の両方で、より多くのクエリテストを実行します。
  * テスト名とファクタリングが変更されました。これらのテストを使用して応答するには、プロバイダーが必要です
* <https://github.com/dotnet/efcore/pull/12766> -ComplexNavigations モデルのナビゲーションの名前変更
  * これらのテストを使用するプロバイダーは、
* <https://github.com/dotnet/efcore/pull/12141> -機能テストで破棄するのではなく、プールにコンテキストを返します。
  * この変更には、プロバイダーに対応するために必要なテストリファクタリングが含まれています。

### <a name="test-and-product-code-changes"></a>テストと製品コードの変更

* <https://github.com/dotnet/efcore/pull/12109> -RelationalTypeMapping メソッドを統合する
  * 2.1 の変更は、派生クラスの単純化に使用できる RelationalTypeMapping に変更されました。 これがプロバイダーによって中断されているとは限りませんが、派生型のマッピングクラスでは、プロバイダーがこの変更を利用できます。
* <https://github.com/dotnet/efcore/pull/12069> -タグ付きまたは名前付きクエリ
  * LINQ クエリにタグを付け、それらのタグを SQL にコメントとして表示するためのインフラストラクチャを追加します。 これには、プロバイダーが SQL 生成時に応答する必要がある場合があります。
* <https://github.com/dotnet/efcore/pull/13115> -NTS を使用した空間データのサポート
  * 型マッピングとメンバートランスレーターをプロバイダーの外部に登録できるようにします
    * プロバイダーは base を呼び出す必要があります。ITypeMappingSource 実装で FindMapping () を使用するための実装
  * プロバイダー間で一貫した空間サポートをプロバイダーに追加するには、このパターンに従います。
* <https://github.com/dotnet/efcore/pull/13199> -サービスプロバイダーの作成用に強化されたデバッグ機能を追加する
  * 内部サービスプロバイダーが再構築されている理由を理解するのに役立つ新しいインターフェイスを DbContextOptionsExtensions が実装できるようにします。
* <https://github.com/dotnet/efcore/pull/13289> -正常性チェックで使用する CanConnect API を追加します
  * この PR は、 `CanConnect` データベースが使用可能かどうかを判断するために ASP.NET Core の正常性チェックで使用されるの概念を追加します。 既定では、リレーショナル実装はを呼び出すだけ `Exist` ですが、プロバイダーは必要に応じて別のものを実装できます。 非リレーショナルプロバイダーは、正常性チェックを使用できるようにするために、新しい API を実装する必要があります。
* <https://github.com/dotnet/efcore/pull/13306> -DbParameter サイズを設定しないように base RelationalTypeMapping を更新します
  * 既定では、切り捨てが発生する可能性があるため、サイズの設定を停止します。 サイズを設定する必要がある場合、プロバイダーは独自のロジックを追加する必要があります。
* <https://github.com/dotnet/efcore/pull/13372> -RevEng: 常に decimal 型の列に対して列の型を指定します
  * スキャフォールディングコードでは、規則によって構成するのではなく、常に decimal 型の列に対して列の型を構成します。
  * プロバイダーの最後に変更を加える必要はありません。
* <https://github.com/dotnet/efcore/pull/13469> -SQL CASE 式を生成するための CaseExpression を追加します
* <https://github.com/dotnet/efcore/pull/13648> -引数と結果のストア型の推定を向上させるために、SqlFunctionExpression で型マッピングを指定する機能を追加します。
