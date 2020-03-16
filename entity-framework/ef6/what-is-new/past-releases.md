---
title: 過去のリリースの Entity Framework-EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: b7181334cd125c5cbf296d5b3674c0b5f087f438
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2020
ms.locfileid: "79402121"
---
# <a name="past-releases-of-entity-framework"></a>過去のリリースの Entity Framework

Entity Framework の最初のバージョンは、.NET Framework 3.5 SP1 と Visual Studio 2008 SP1 の一部として2008にリリースされました。

EF 4.1 リリース以降、 [Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)として出荷されました。現在、NuGet.org で最も人気のあるパッケージの1つです。

バージョン4.1 と5.0 の間で、EntityFramework NuGet パッケージは .NET Framework の一部として出荷された EF ライブラリを拡張しています。

バージョン6以降では、EF はオープンソースプロジェクトになり、.NET Framework から完全に帯域外に移動されました。
これは、EntityFramework version 6 NuGet パッケージをアプリケーションに追加するときに、.NET Framework の一部として出荷される EF ビットに依存しない EF ライブラリの完全なコピーを取得することを意味します。
これにより、新機能の開発と配信のペースが少し加速します。

2016年6月に、EF Core 1.0 がリリースされました。 EF Core は、新しいコードベースに基づいており、より軽量で拡張可能な EF バージョンとして設計されています。
現在 EF Core は、Microsoft で Entity Framework チームを対象とした開発の主な分野です。
つまり、EF6 に対して計画されている新しい主要な機能はありません。 ただし、EF6 は依然としてオープンソースプロジェクトおよびサポートされている Microsoft 製品として維持されます。

次に示すのは、過去のリリースと、各リリースで導入された新機能に関する情報の逆の順序です。

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Visual Studio 2017 15.7 の EF ツールの更新プログラム
2018 年 5 月に、Visual Studio 2017 15.7 の一部として EF ツールの更新バージョンをリリースしました。
これには、次のような一般的な問題点の機能強化が含まれています。

- ユーザー インターフェイス アクセシビリティに関するいくつかのバグの修正プログラム
- 既存のデータベースからモデルを生成するときの SQL Server のパフォーマンス低下の回避策 [#4](https://github.com/aspnet/entityframework6/issues/4)
- SQL Server での大規模モデルに対するモデル更新のサポート [#185](https://github.com/aspnet/EntityFramework6/issues/185)

この EF ツールの新しいバージョンの別の改善点として、新しいプロジェクトでのモデル作成時に EF 6.2 ランタイムがインストールされます。 以前のバージョンの Visual Studio では、NuGet パッケージの対応するバージョンをインストールすることで、EF 6.2 ランタイム (と過去のすべてのバージョンの EF) を使用することができます。

## <a name="ef-620"></a>EF 6.2.0
EF 6.2 ランタイムは、NuGet で 2017 年 10 月にリリースされました。
オープン ソースの共同作成者のコミュニティによる多大な尽力のおかげで、EF 6.2 には多数の[バグ修正](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug)と[製品の拡張機能](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20)が含まれています。

EF 6.2 ランタイムに影響を与える、特に重要な変更の簡単な一覧を次に示します。

- 永続的キャッシュから完了済みコードの最初のモデルを読み込むことで、起動時間が短縮される [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- インデックスを定義する fluent API [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- SQL で LIKE に変換される LINQ クエリの記述を有効にするための DbFunctions.Like() [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe で -script オプションがサポート [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 で SQL Server のシーケンスで生成されたキーの値が使用可能に [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- SQL Azure の実行方法に関する一時的なエラーのリストの更新 [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- バグ: クエリまたは SQL コマンドの再試行が "The SqlParameter is already contained by another SqlParameterCollection" (この SqlParameter は別の SqlParameterCollection に既に含まれています) で失敗する [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- バグ: DbQuery.ToString() の評価がデバッガーで頻繁にタイムアウトになる [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF EF6.1.3
EF ef6.1.3 runtime は2015年10月に NuGet にリリースされました。
このリリースには、6.1.2 リリースで報告される優先度の高い欠陥と回帰に対する修正のみが含まれています。
次のような修正が行われます。

- クエリ: EF 6.1.2: OUTER APPLY では、1:1 リレーションシップと "let" 句に対してより複雑なクエリが導入されました。
- 継承されたクラスの基底クラスプロパティを非表示にすると TPT の問題が発生する
- DbMigration。テキストに "進む" という単語が含まれていると、Sql が失敗する
- UnionAll と Intersect のフラット化サポートの互換性フラグを作成する
- 複数のインクルードを含むクエリが6.1.2 で機能しない (6.1.1 で動作)
- EF 6.1.1 から6.1.2 へのアップグレード後に "SQL 構文にエラーが発生しました" という例外が発生する

## <a name="ef-612"></a>EF 6.1.2
EF 6.1.2 runtime は、2014年12月に NuGet にリリースされました。
このバージョンは、ほとんどがバグの修正に関するものです。 また、コミュニティのメンバーから、いくつかの注目すべき変更が認められています。
- **クエリキャッシュパラメーターは、アプリ/web 構成ファイルから構成できます。**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **DbMigration の Sqlfile メソッドと Sqlfile メソッド**を使用すると、ファイルまたは埋め込みリソースとして格納されている SQL スクリプトを実行できます。

## <a name="ef-611"></a>EF 6.1.1
EF 6.1.1 runtime は、2014年6月に NuGet にリリースされました。
このバージョンには、多数の担当者が遭遇した問題の修正が含まれています。 その他:
- デザイナー: EF6 デザイナーで EF5 edmx を10進精度で開くときにエラーが発生する
- LocalDB の既定のインスタンス検出ロジックが SQL Server 2014 で動作しない

## <a name="ef-610"></a>EF 6.1.0
EF 6.1.0 runtime は2014年3月に NuGet にリリースされました。
このマイナー更新には、多くの新機能が含まれています。

- **ツールの統合**により、新しい EF モデルを一貫した方法で作成できます。 この機能[は、ADO.NET Entity Data Model ウィザードを拡張して](~/ef6/modeling/code-first/workflows/existing-database.md)、既存のデータベースからのリバースエンジニアリングを含む Code First モデルの作成をサポートします。 これらの機能は、以前は EF パワーツールのベータ品質で使用できました。
- **[トランザクションのコミットエラーの処理](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** では、トランザクション操作をインターセプトする新しく導入された機能を使用する CommitFailureHandler が提供されます。 CommitFailureHandler を使用すると、トランザクションのコミット中に、接続エラーからの自動復旧を行うことができます。
- **[Indexattribute](~/ef6/modeling/code-first/data-annotations.md)** を使用すると、Code First モデルのプロパティ (またはプロパティ) に `[Index]` 属性を配置することで、インデックスを指定できます。 Code First によって、データベースに対応するインデックスが作成されます。
- **パブリックマッピング API は**、プロパティおよび型をデータベース内の列およびテーブルにマップする方法について、EF が使用する情報にアクセスできるようにします。 過去のリリースでは、この API は内部的でした。
- **[アプリ/web.config ファイルを使用してインターセプターを構成する機能](~/ef6/fundamentals/configuring/config-file.md)** を使用すると、アプリケーションを再コンパイルしなくてもインターセプターを追加できます。
- System.string は、すべてのデータベース操作をファイルに簡単に記録できるようにする新しいインターセプターとなって**います。** 以前の機能と組み合わせて使用すると、配置された[アプリケーションのデータベース操作のログ記録](~/ef6/fundamentals/configuring/config-file.md)を簡単に切り替えることができ、再コンパイルする必要はありません。
- **移行モデル変更の検出**が改善され、スキャフォールディングの移行がより正確になりました。また、変更検出プロセスのパフォーマンスも向上しています。
- 初期化中のデータベース操作の削減、LINQ クエリでの null 等値比較の最適化、より多くのシナリオでのより高速なビュー生成 (モデルの作成)、複数のアソシエーションを持つ追跡対象エンティティのより効率的な具体化など、**パフォーマンスの向上**。

## <a name="ef-602"></a>EF 6.0.2
EF 6.0.2 runtime は、2013年12月に NuGet にリリースされました。
この修正プログラムのリリースは、EF6 リリースで導入された問題の修正 (EF5 以降のパフォーマンス/動作の回帰) に限定されています。

## <a name="ef-601"></a>EF 6.0.1
EF 6.0.1 ランタイムは、2013年10月に、EF 6.0.0 と同時に NuGet にリリースされました。後者は、数カ月前にロックされていたバージョンの Visual Studio に埋め込まれていたためです。
この修正プログラムのリリースは、EF6 リリースで導入された問題の修正 (EF5 以降のパフォーマンス/動作の回帰) に限定されています。
最も注目すべき変更点は、EF モデルのウォームアップ中にパフォーマンスの問題を修正することでした。
これは、ウォームアップのパフォーマンスが EF6 の分野であり、これらの問題が EF6 に加えられた他のパフォーマンスの向上を否定したために重要でした。

## <a name="ef-60"></a>EF 6.0
EF 6.0.0 runtime は2013年10月に NuGet にリリースされました。
これは、完全な EF ランタイムが[Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)に含まれている最初のバージョンです。これは、.NET Framework の一部である ef ビットに依存しません。
ランタイムの残りの部分を NuGet パッケージに移動するには、既存のコードに多くの重大な変更を加える必要があります。
アップグレードに必要な手動手順の詳細については、 [Entity Framework 6 へのアップグレード](upgrading-to-ef6.md)に関するセクションを参照してください。

このリリースには、多くの新機能が含まれています。
Code First または EF デザイナーで作成されたモデルでは、次の機能が使用できます。

- **[非同期クエリと保存](~/ef6/fundamentals/async.md)** では、.net 4.5 で導入されたタスクベースの非同期パターンのサポートが追加されます。
- **[接続の回復性](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** を使用すると、一時的な接続エラーからの自動復旧が可能になります。
- **[コードベースの構成](~/ef6/fundamentals/configuring/code-based.md)** では、構成を実行するオプションが用意されています。これは、従来は構成ファイルで実行されていたコードです。
- **[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)** では、サービスロケーターパターンのサポートが導入されており、カスタム実装に置き換えることができるいくつかの機能を考慮しています。
- **[インターセプトと sql のログ記録](~/ef6/fundamentals/logging-and-interception.md)** は、上位に構築された単純な SQL ログを使用して、EF 操作を傍受するための低レベルの構成ブロックを提供します。
- テストの容易性の**向上**により、[モックフレームワークを使用](~/ef6/fundamentals/testing/mocking.md)する場合や[独自のテスト double](~/ef6/fundamentals/testing/writing-test-doubles.md)を作成する場合に、dbcontext および dbcontext のテスト代替を簡単に作成できるようになりました。
- **[既に開かれている DbConnection を使用して Dbcontext を作成できるようになりまし](~/ef6/fundamentals/connection-management.md)** た。これにより、コンテキストの作成時に接続を開くことができた場合 (接続の状態を保証できないコンポーネント間の接続の共有など) に役立つシナリオが有効になります。
- **[トランザクションサポートの強化](~/ef6/saving/transactions.md)** により、フレームワークの外部にあるトランザクションのサポートに加え、フレームワーク内でトランザクションを作成するための向上した方法が提供されます。
- **.Net 4.0 での列挙型、空間、およびパフォーマンスの向上**-.NET Framework に含まれていたコアコンポーネントを EF NuGet パッケージに移動することで、enum サポート、空間データ型、.net 4.0 での EF5 のパフォーマンス向上を実現できるようになりました。
- **列挙型のパフォーマンスが向上しました。 LINQ クエリでが含まれています。**
- 特に大規模なモデルでの**ウォームアップ時間の向上 (ビュー生成)** 。
- **プラグ可能な複数形化 &amp; 単数サービス**。
- エンティティクラスの**Equals または GetHashCode のカスタム実装**がサポートされるようになりました。
- **Dbset. AddRange/removerange**よって、セットから複数のエンティティを追加または削除するための最適化された方法が提供されます。
- **Dbchangetracker. HasChanges**を使用すると、データベースに保存されている保留中の変更があるかどうかを簡単かつ効率的に確認できます。
- **SqlCeFunctions**は、sqlfunctions に相当する SQL Compact を提供します。

次の機能は Code First にのみ適用されます。

- **[カスタム Code First 規則](~/ef6/modeling/code-first/conventions/custom.md)** を使用すると、繰り返し構成を避けるために独自の規則を記述できます。 簡易規則用の単純な API と、より複雑な規則を作成できるようにする複雑な構成要素が用意されています。
- **[ストアドプロシージャの挿入/更新/削除への Code First のマッピング](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** がサポートされるようになりました。
- **[べき等移行スクリプト](~/ef6/modeling/code-first/migrations/index.md)** を使用すると、最新バージョンまでの任意のバージョンのデータベースをアップグレードできる SQL スクリプトを生成できます。
- **[構成可能な移行履歴テーブル](~/ef6/modeling/code-first/migrations/history-customization.md)** を使用すると、移行履歴テーブルの定義をカスタマイズできます。 これは、適切なデータ型を必要とするデータベースプロバイダーや、移行履歴テーブルが正常に機能するように指定するために特に便利です。
- 1つのデータベースに**複数のコンテキスト**がある場合、移行を使用する場合、またはデータベースを自動的に作成 Code First 場合に、データベースごとに1つの Code First モデルの以前の制限が削除されます。
- **[Dbmodelbuilder. HasDefaultSchema](~/ef6/modeling/code-first/fluent/types-and-properties.md)** は、Code First モデルの既定のデータベーススキーマを1か所で構成できる新しい Code First API です。 以前は、Code First の既定のスキーマは &quot;dbo&quot; にハードコーディングされており、テーブルが属していたスキーマを構成する唯一の方法は ToTable API を使用していました。
- **AddFromAssembly メソッド**を使用すると、CODE FIRST Fluent API で構成クラスを使用している場合に、アセンブリで定義されているすべての構成クラスを簡単に追加できます。
- **[カスタム移行操作](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** を使用すると、コードベースの移行で使用する操作を追加できます。
- Code First を使用して作成されたデータベースで**は、既定のトランザクション分離レベルが READ_COMMITTED_SNAPSHOT に変更**されます。これにより、スケーラビリティが向上し、デッドロックが減少します。
- **エンティティ型と複合型を nestedinside クラスにすることができるようになりました**。

## <a name="ef-50"></a>EF 5.0
EF 5.0.0 runtime は、2012年8月に NuGet にリリースされました。
このリリースでは、列挙型のサポート、テーブル値関数、空間データ型、さまざまなパフォーマンスの向上など、いくつかの新機能が導入されています。

また、Visual Studio 2012 の Entity Framework Designer では、モデルごとに複数のダイアグラムがサポートされるようになりました。また、デザイン画面での図形の色分けとストアドプロシージャの一括インポートも導入されています。

EF 5 リリース専用にまとめられたコンテンツの一覧を次に示します。

-   [EF 5 リリース投稿](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 の新機能
    -   [Code First での列挙のサポート](~/ef6/modeling/code-first/data-types/enums.md)
    -   [EF デザイナーでの列挙のサポート](~/ef6/modeling/designer/data-types/enums.md)
    -   [Code First の空間データ型](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [EF デザイナーの空間データ型](~/ef6/modeling/designer/data-types/spatial.md)
    -   [空間型のプロバイダーサポート](~/ef6/fundamentals/providers/spatial-support.md)
    -   [テーブル値関数](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [モデルごとに複数のダイアグラム](~/ef6/modeling/designer/multiple-diagrams.md)
-   モデルの設定
    -   [モデルの作成](~/ef6/modeling/index.md)
    -   [接続とモデル](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [パフォーマンスに関する考慮事項](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [Microsoft SQL Azure の使用](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [構成ファイルの設定](~/ef6/fundamentals/configuring/config-file.md)
    -   [用語集](~/ef6/resources/glossary.md)
    -   Code First
        -   [新しいデータベースへの Code First (チュートリアルとビデオ)](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [既存のデータベースへの Code First (チュートリアルとビデオ)](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [規則](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [データの注釈](~/ef6/modeling/code-first/data-annotations.md)
        -   [Fluent API-型 & プロパティの構成/マッピング](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [Fluent API-リレーションシップの構成](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [VB.NET を使用した Fluent API](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)
        -   [自動 Code First Migrations](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [移行 .exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [定義 (DbSets を)](~/ef6/modeling/code-first/dbsets.md)
    -   EF デザイナー
        -   [Model First (チュートリアルとビデオ)](~/ef6/modeling/designer/workflows/model-first.md)
        -   [Database First (チュートリアルとビデオ)](~/ef6/modeling/designer/workflows/database-first.md)
        -   [複合型](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [アソシエーション/リレーションシップ](~/ef6/modeling/designer/relationships.md)
        -   [TPT 継承パターン](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [TPH 継承パターン](~/ef6/modeling/designer/inheritance/tph.md)
        -   [ストアドプロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [複数の結果セットを持つストアドプロシージャ](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [ストアドプロシージャを使用した Insert、Update & Delete](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [複数のテーブルへのエンティティのマップ (エンティティ分割)](~/ef6/modeling/designer/entity-splitting.md)
        -   [複数のエンティティを1つのテーブルにマップする (テーブル分割)](~/ef6/modeling/designer/table-splitting.md)
        -   [クエリの定義](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [コード生成テンプレート](~/ef6/modeling/designer/codegen/index.md)
        -   [ObjectContext に戻す](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   モデルの使用
    -   [DbContext の操作](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [エンティティの照会/検索](~/ef6/querying/index.md)
    -   [リレーションシップの操作](~/ef6/fundamentals/relationships.md)
    -   [関連エンティティを読み込んでいます](~/ef6/querying/related-data.md)
    -   [ローカルデータの操作](~/ef6/querying/local-data.md)
    -   [N 層アプリケーション](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [生 SQL クエリ](~/ef6/querying/raw-sql.md)
    -   [オプティミスティック同時実行制御パターン](~/ef6/saving/concurrency.md)
    -   [プロキシの使用](~/ef6/fundamentals/proxies.md)
    -   [自動検出の変更](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [追跡なしのクエリ](~/ef6/querying/no-tracking.md)
    -   [Load メソッド](~/ef6/querying/load-method.md)
    -   [追加/アタッチとエンティティの状態](~/ef6/saving/change-tracking/entity-state.md)
    -   [プロパティ値の操作](~/ef6/saving/change-tracking/property-values.md)
    -   [WPF を使用したデータバインディング (Windows Presentation Foundation)](~/ef6/fundamentals/databinding/wpf.md)
    -   [WinForms を使用したデータバインディング (Windows フォーム)](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3.1
Ef 4.3.1 ランタイムは、EF 4.3.0 の後すぐに2012年2月に NuGet にリリースされました。
この修正プログラムのリリースには、EF 4.3 リリースにいくつかのバグ修正が含まれており、Visual Studio 2012 で EF 4.3 を使用しているお客様に対して、より優れた LocalDB サポートを導入

Ef 4.3.1 リリース専用にまとめたコンテンツの一覧を次に示します。 ef 4.1 用に提供されているコンテンツの大部分は EF 4.3 にも適用されます。

-   [EF 4.3.1 リリースのブログ投稿](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4.3
EF 4.3.0 ランタイムは、2012年2月に NuGet にリリースされました。
このリリースには、Code First モデルの進化に伴って Code First によって作成されたデータベースの増分変更を可能にする新しい Code First Migrations 機能が含まれていました。

Ef 4.3 リリース専用にまとめたコンテンツの一覧を次に示します。 ef 4.1 用に提供されているコンテンツの大部分は、ef 4.3 にも適用されます。
-   [EF 4.3 リリースの投稿](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 コードベースの移行のチュートリアル](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 自動移行のチュートリアル](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4.2
EF 4.2.0 ランタイムは、2011年11月に NuGet にリリースされました。
このリリースには、EF 4.1.1 リリースのバグ修正が含まれています。
このリリースにはバグ修正が含まれているだけなので、EF 4.1.2 patch リリースになっている可能性がありますが、4.2 に移行することにしました。これにより、4.1 リリースで使用していた日付ベースのパッチバージョン番号から移動し、セマンティックバージョン管理[のセマンティックバージョン](https://semver.org)を採用します。

Ef 4.2 リリース専用にまとめたコンテンツの一覧を次に示します。 ef 4.1 用に提供されているコンテンツは、ef 4.2 にも引き続き適用されます。

-   [EF 4.2 リリースの投稿](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Code First チュートリアル](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [モデル & Database First チュートリアル](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
EF 4.1.10715 runtime は2011年7月に NuGet にリリースされました。
バグの修正に加えて、この修正プログラムのリリースでは、デザイン時のツールで Code First モデルを操作しやすくするために、いくつかのコンポーネントが導入されました。
これらのコンポーネントは Code First Migrations (EF 4.3 に含まれています) と EF パワーツールによって使用されます。

パッケージのバージョン番号が奇妙に4.1.10715 ていることがわかります。
[セマンティックバージョン管理](https://semver.org)を採用する前に、日付ベースのパッチバージョンを使用することにしました。
このバージョンは、EF 4.1 patch 1 (または EF 4.1.1) と考えてください。

次に、4.1.1 リリース用にまとめたコンテンツの一覧を示します。

-   [EF 4.1.1 リリース投稿](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4.1
EF 4.1.10331 runtime は、2011年4月に NuGet で公開される最初のランタイムでした。
このリリースには、簡略化された DbContext API と Code First ワークフローが含まれていました。

予期しないバージョン番号4.1.10331 が表示されます。これは、実際には4.1 である必要があります。 さらに、4.1.10311 のバージョンがあります。これは、4.1.0-rc である必要があります (' rc ' は ' リリース候補 ' を表します)。
[セマンティックバージョン管理](https://semver.org)を採用する前に、日付ベースのパッチバージョンを使用することにしました。

4\.1 リリース用にまとめたコンテンツの一覧を次に示します。 その多くは、Entity Framework の今後のリリースにも適用されます。

-   [EF 4.1 リリースの投稿](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Code First チュートリアル](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [モデル & Database First チュートリアル](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure フェデレーションと Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4.0
このリリースは .NET Framework 4 および Visual Studio 2010 (2010 年4月) に含まれています。
このリリースの重要な新機能には、POCO サポート、外部キーマッピング、遅延読み込み、テストの容易性の向上、カスタマイズ可能なコード生成、および Model First ワークフローが含まれています。

Entity Framework の2番目のリリースでしたが、それに付属していた .NET Framework バージョンに合わせて EF 4 という名前が付けられました。
このリリースの後、.NET Framework バージョンに関連付けられなくなったため、NuGet で Entity Framework 使用できるようにし、セマンティックバージョン管理を採用しました。

それ以降のバージョンの .NET Framework には、含まれている EF ビットに対する重要な更新プログラムが付属しています。
実際、EF 5.0 の新機能の多くは、これらのビットの機能強化として実装されています。
ただし、EF のバージョン管理のストーリーを合理化するために、ef 4.0 ランタイムとして .NET Framework の一部である EF ビットを引き続き参照しますが、新しいバージョンはすべて[Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)で構成されています。

## <a name="ef-35"></a>EF 3.5
Entity Framework の初期バージョンは、3.5 2008 年8月にリリースされた .NET Service Pack 1 および Visual Studio 2008 SP1 に含まれていました。
このリリースでは、Database First ワークフローを使用した基本的な O/RM サポートが提供されています。
