---
title: Entity Framework の EF6 の過去のリリース
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 1060bb99-765f-4f32-aaeb-d6635d3dbd3e
caps.latest.revision: 4
ms.openlocfilehash: 5be3632fd3a3f04e12e2d3aa67de6c1d9c7b56a2
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122809"
---
# <a name="past-releases-of-entity-framework"></a>Entity Framework の過去のリリース

Entity Framework の最初のバージョンは、.NET Framework 3.5 SP1 と Visual Studio 2008 SP1 の一部として、2008 年にリリースされました。

として出荷されましたが、EF4.1 リリース以降、 [EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)-現在、NuGet.org の最も一般的なパッケージの 1 つ。

バージョン 4.1、5.0、間は、EntityFramework NuGet パッケージは、.NET Framework の一部として同梱されている EF ライブラリを拡張します。   

バージョン 6 以降、EF はオープン ソース プロジェクトのようになりましたし、移動も完全に帯域外フォーム、.NET Framework します。
これは、アプリケーションに entity Framework 6 のバージョンの NuGet パッケージを追加すると、.NET Framework の一部として出荷される EF ビットに依存しない EF ライブラリの完全なコピーを得られることを意味します。
これにより、ある程度の開発のペースと新機能の配信を高速化します。

2016 年 6 月では、EF Core 1.0 をリリースしました。 EF Core は新しいコードベースに基づいておりより軽量で拡張可能なバージョンの EF として設計されています。
現在 EF Core では、マイクロソフトの Entity Framework チームの開発の中心です。
つまり、EF6 の計画的な新しいの主な機能はありません。 ただし EF6 はオープン ソース プロジェクトおよびサポートされている Microsoft 製品でも維持されます。

各リリースで導入された新機能に関する情報を逆時系列順で過去のリリースの一覧を示します。

## <a name="ef-613"></a>EF 6.1.3
6.1.3 EF ランタイムは、NuGet を 2015 年 10 月にリリースされました。
このリリースには、優先度の高い問題と、6.1.2 で報告された回帰を修正プログラムのみが含まれています。 リリースします。
修正プログラムは次のとおりです。

- クエリ: ef 6.1.2 回帰: OUTER APPLY を導入し、1 対 1 リレーションシップと"let"句のより複雑なクエリ
- TPT 継承クラスで基底クラスのプロパティを非表示の問題
- DbMigration.Sql 'go' という単語がテキストに含まれている場合が失敗します。
- UnionAll と最高のサポート Intersect 互換性フラグを作成します。
- 6.1.2 (6.1.1 での作業) で複数のインクルードを使用したクエリは機能しません
- EF 6.1.1 を 6.1.2 からアップグレードした後、「があるエラー、SQL 構文では」例外

## <a name="ef-612"></a>EF 6.1.2
6.1.2 EF ランタイムは、2014 年 12 月の NuGet にリリースされました。
このバージョンは、バグの修正に関するほとんどの場合は。 いくつかのコミュニティ メンバーから注目に値する変更も受け入れ済み。
- **App/web.configuration ファイルからキャッシュのクエリ パラメーターを構成することができます。**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- **SqlFile と SqlResource DbMigration メソッド**SQL を実行できるスクリプト ファイルとして格納されているか、埋め込みリソース。

## <a name="ef-611"></a>EF 6.1.1
EF 6.1.1 ランタイムは、2014 年 6 月の NuGet にリリースされました。
このバージョンには、問題が発生したユーザーの数の修正が含まれています。 その他。
- EF6 のデザイナーでの小数点以下有効桁数に EF5 edmx を開くときにデザイナー: エラー
- LocalDB の既定のインスタンスの検出ロジックは、SQL Server 2014 を使用しません。

## <a name="ef-610"></a>EF 6.1.0
EF 6.1.0 ランタイムは、2014 年 3 月の NuGet にリリースされました。
このマイナー更新には、多数新機能にはが含まれます。

- **ツールの統合**新しい EF モデルを作成する一貫した方法を提供します。 この機能[Code First モデルの作成をサポートするために、ADO.NET Entity Data Model ウィザードを拡張](~/ef6/modeling/code-first/workflows/existing-database.md)、既存のデータベースからなどのリバース エンジニア リングします。 これらの機能では、以前の EF Power Tools Beta 品質で利用できます。
- **[トランザクションのコミット エラーの処理の](~/ef6/fundamentals/connection-resiliency/commit-failures.md)** を使用する新しく導入された機能のトランザクションの操作を傍受する CommitFailureHandler を提供します。 CommitFailureHandler は、トランザクションのコミット中に、接続の障害からの自動復旧をできます。
- **[IndexAttribute](~/ef6/modeling/code-first/data-annotations.md)** により、インデックスを配置することで指定する、 `[Index]` Code First モデルのプロパティ (またはプロパティ) の属性します。 コード最初はインデックスを作成して対応するデータベースにします。
- **パブリック マッピング API** EF がデータベースで列とテーブルにプロパティと型をマップする方法に情報へのアクセスを提供します。 以前のリリースこの API が内部です。
- **[App/Web.config ファイルを使用してインターセプターを構成する機能](~/ef6/fundamentals/configuring/config-file.md)** アプリケーションを再コンパイルせずに追加するインターセプターを使用します。
- **System.Data.Entity.Infrastructure.Interception.DatabaseLogger**をファイルにすべてのデータベース操作を記録するが容易にする新しいインターセプターします。 従来の機能と組み合わせて、することができます簡単に[デプロイされたアプリケーションのデータベース操作のログ記録を切り替える](~/ef6/fundamentals/configuring/config-file.md)、再コンパイルする必要はありません。
- **移行モデル変更の検出**スキャフォールディングの移行がより正確な; 変更の検出プロセスのパフォーマンスが強化されてもようにが改善されました。
- **パフォーマンスの向上**の初期化中に、制限のデータベース操作など、LINQ クエリで null の等値比較のための最適化高速化 (モデルの作成) の生成で表示するより多くのシナリオより効率的な複数のアソシエーションを持つ追跡対象エンティティの具体化します。

## <a name="ef-602"></a>EF 6.0.2
6.0.2 EF ランタイムは、NuGet に 2013 年 12 月にリリースされました。
この修正プログラムのリリースに導入された問題の解決に制限されていますが、EF6 リリース (パフォーマンス/動作に回帰 EF5 以降) でします。

## <a name="ef-601"></a>EF 6.0.1 を選ぶ
6.0.1 EF ランタイムは、EF 6.0.0、同時に、2013 年 10 月に、後者の前に、数か月がロックされている Visual Studio のバージョンに埋め込まれたためにで、NuGet にリリースされました。
この修正プログラムのリリースに導入された問題の解決に制限されていますが、EF6 リリース (パフォーマンス/動作に回帰 EF5 以降) でします。
最も注目すべき変更は、EF モデルのウォーム アップ時にパフォーマンスの問題を修正するでした。
これは、EF6 とこれらの問題へのフォーカスの領域がいくつかの EF6 で行われたその他のパフォーマンスの向上に否定された際にウォーム アップのパフォーマンスが重要です。

## <a name="ef-60"></a>EF 6.0
EF 6.0.0 ランタイムは、NuGet に 2013 年 10 月にリリースされました。
これは、完全な EF ランタイムに含まれている最初のバージョン、 [EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)が .NET Framework の一部である EF ビットには依存しません。
NuGet パッケージに、ランタイムの残りの部分を移動すると、既存コードに対する変更の重大な数が必要です。
参照してください[Entity Framework 6 にアップグレードする](upgrading-to-ef6.md)アップグレードに必要な手動の手順の詳細についてはします。

このリリースには、多くの新しい機能が含まれています。
次の機能は Code First または EF Designer で作成されたモデルに対して動作します。

- **[非同期クエリと保存](~/ef6/fundamentals/async.md)** .NET 4.5 で導入されたタスクベースの非同期パターンのサポートを追加します。
- **[接続の回復性](~/ef6/fundamentals/connection-resiliency/retry-logic.md)** 一時的な接続の障害からの自動復旧できるようにします。
- **[コード ベースの構成](~/ef6/fundamentals/configuring/code-based.md)** – – 構成ファイルで実行された従来の構成を実行するコードでのオプションを選択できます。
- **[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)** サポートが導入され、サービス ロケーターのパターンとがカスタム実装に置き換えることができる機能のいくつかの部分を考慮しました。
- **[SQL インターセプション/ログ](~/ef6/fundamentals/logging-and-interception.md)** 上に構築される SQL ログ記録は、常に単純な EF の操作の傍受のための低レベルの構成要素を提供します。
- **テストの容易性の機能強化**やすく DbContext、DbSet のテスト代替を作成するときに[モック作成フレームワークを使用して](~/ef6/fundamentals/testing/mocking.md)または[独自のテスト代替](~/ef6/fundamentals/testing/writing-test-doubles.md)。
- **[DbContext が既に開かれている DbConnection で作成できます](~/ef6/fundamentals/connection-management.md)** これにより、シナリオのどこにあるべき便利な場合は、コンテキストを作成するときに、接続が開いている可能性があります (コンポーネント間の接続を共有するなど、保証できません接続の状態)。
- **[トランザクションのサポートを改善](~/ef6/saving/transactions.md)** フレームワークとフレームワーク内でのトランザクションを作成する方法が改善された外部トランザクションのサポートを提供します。
- **列挙型、空間、および .NET 4.0 でパフォーマンスが向上**- .NET に EF5 から列挙型のサポート、空間データ型およびパフォーマンスの向上を提供することがこれで、EF の NuGet パッケージに .NET Framework にするために使用するコア コンポーネントを移動することによって4.0。
- **Enumerable.Contains の LINQ クエリでのパフォーマンスの向上**します。
- **ウォーム アップ時間 (ビューの生成) が強化された**、特に大規模なモデルの場合。
- **プラグ可能な複数形化&amp;単サービス**します。
- **Equals や GetHashCode のカスタム実装**エンティティのクラスがサポートされています。
- **DbSet.AddRange/RemoveRange**を追加または複数のエンティティをセットから削除、最適化された方法を提供します。
- **DbChangeTracker.HasChanges**データベースに保存する保留中の変更があるかを簡単かつ効率的な方法を提供します。
- **SqlCeFunctions**は、SQL Compact、SqlFunctions に相当します。

次の機能は、Code First にのみ適用されます。

- **[カスタム コードの最初規則](~/ef6/modeling/code-first/conventions/custom.md)** 許可反復的な構成を回避するための独自の規則を作成します。 複雑な規則を作成できるようにする軽量な規則としていくつかのより複雑な構成要素の単純な API を提供します。
- **[コードの挿入/更新/削除ストアド プロシージャへの最初のマッピング](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)** がサポートされています。
- **[べき等である移行スクリプト](~/ef6/modeling/code-first/migrations/index.md)** 最新バージョンのいずれかのバージョンでデータベースをアップグレードできる SQL スクリプトを生成できます。
- **[構成可能な移行履歴テーブル](~/ef6/modeling/code-first/migrations/history-customization.md)** 移行履歴テーブルの定義をカスタマイズすることができます。 これは、適切なデータ型などを正常に動作する移行履歴テーブルの指定を必要とするデータベース プロバイダーに特に便利です。
- **データベースごとの複数のコンテキスト**Migrations を使用しているときに、データベースごと、または Code First 自動的に作成されたときに、データベースの Code First モデルを 1 つの以前の制限を削除します。
- **[DbModelBuilder.HasDefaultSchema](~/ef6/modeling/code-first/fluent/types-and-properties.md)** は新しい Code First API を 1 か所で構成する Code First モデルの既定のデータベース スキーマを許可します。 以前のコードの最初の既定のスキーマにハード コードされた&quot;dbo&quot; ToTable API を使用して、テーブルが所属するスキーマを構成する唯一の方法です。
- **DbModelBuilder.Configurations.AddFromAssembly メソッド**Code First Fluent API を構成クラスを使用しているときに、アセンブリで定義されているすべての構成クラスを簡単に追加することができます。
- **[カスタムの移行操作](http://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** コード ベースの移行で使用される追加の操作を追加することができます。
- **既定のトランザクション分離レベル READ_COMMITTED_SNAPSHOT は**Code First で、拡張性やデッドロックを少なくすることができますを使用して作成されたデータベース。
- **エンティティと複合型できるようになりました nestedinside クラス**します。 |

## <a name="ef-50"></a>EF 5.0
EF 5.0.0 ランタイムは、2012 年 8 月の NuGet にリリースされました。
このリリースでは、列挙型のサポート、テーブル値関数、空間データ型、およびさまざまなパフォーマンスの向上を含むいくつかの新機能について説明します。

Visual Studio 2012 での Entity Framework デザイナーは、1 つのモデルでは、複数のダイアグラムのサポートも導入されています。 ストアド プロシージャのデザイン画面とバッチのインポートの図形の色を指定します。

具体的には、EF 5 のリリースをまとめてコンテンツの一覧を示します。

-   [EF 5 のリリースの投稿](http://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   EF5 の新機能
    -   [コードで最初に列挙型のサポート](~/ef6/modeling/code-first/data-types/enums.md)
    -   [EF Designer で列挙型のサポート](~/ef6/modeling/designer/data-types/enums.md)
    -   [空間データ型は、最初のコード](~/ef6/modeling/code-first/data-types/spatial.md)
    -   [EF デザイナーで空間データ型](~/ef6/modeling/designer/data-types/spatial.md)
    -   [空間型のプロバイダー サポート](~/ef6/fundamentals/providers/spatial-support.md)
    -   [テーブル値関数](~/ef6/modeling/designer/advanced/tvfs.md)
    -   [1 つのモデルの複数のダイアグラム](~/ef6/modeling/designer/multiple-diagrams.md)
-   モデルの設定
    -   [モデルの作成](~/ef6/modeling/index.md)
    -   [接続とモデル](~/ef6/fundamentals/configuring/connection-strings.md)
    -   [パフォーマンスに関する考慮事項](~/ef6/fundamentals/performance/perf-whitepaper.md)
    -   [Microsoft SQL Azure の使用](~/ef6/fundamentals/connection-resiliency/retry-logic.md)
    -   [構成ファイルの設定](~/ef6/fundamentals/configuring/config-file.md)
    -   [用語集](~/ef6/resources/glossary.md)
    -   Code First
        -   [最初にコードを新しいデータベース (チュートリアルとビデオ)](~/ef6/modeling/code-first/workflows/new-database.md)
        -   [最初にコードを既存のデータベース (チュートリアルとビデオ)](~/ef6/modeling/code-first/workflows/existing-database.md)
        -   [規則](~/ef6/modeling/code-first/conventions/built-in.md)
        -   [データの注釈](~/ef6/modeling/code-first/data-annotations.md)
        -   [Fluent API の種類 (&)、プロパティの構成/マッピング](~/ef6/modeling/code-first/fluent/types-and-properties.md)
        -   [Fluent API - 関係の構成](~/ef6/modeling/code-first/fluent/relationships.md)
        -   [VB.NET で Fluent API](~/ef6/modeling/code-first/fluent/vb.md)
        -   [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)
        -   [自動の Code First Migrations](~/ef6/modeling/code-first/migrations/automatic.md)
        -   [Migrate.exe](~/ef6/modeling/code-first/migrations/migrate-exe.md)
        -   [DbSets を定義します。](~/ef6/modeling/code-first/dbsets.md)
    -   EF デザイナー
        -   [モデルの最初 (チュートリアルとビデオ)](~/ef6/modeling/designer/workflows/model-first.md)
        -   [データベース ファースト (チュートリアルとビデオ)](~/ef6/modeling/designer/workflows/database-first.md)
        -   [複合型](~/ef6/modeling/designer/data-types/complex-types.md)
        -   [関連付け/リレーションシップ](~/ef6/modeling/designer/relationships.md)
        -   [TPT 継承パターン](~/ef6/modeling/designer/inheritance/tpt.md)
        -   [TPH 継承パターン](~/ef6/modeling/designer/inheritance/tph.md)
        -   [ストアド プロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)
        -   [複数の結果セットを使用したストアド プロシージャ](~/ef6/modeling/designer/advanced/multiple-result-sets.md)
        -   [挿入、更新ストアド プロシージャを削除する.](~/ef6/modeling/designer/stored-procedures/cud.md)
        -   [エンティティ (エンティティ分割) 複数のテーブルをマップします。](~/ef6/modeling/designer/entity-splitting.md)
        -   [複数のエンティティを 1 つのテーブル (テーブル分割) にマップします。](~/ef6/modeling/designer/table-splitting.md)
        -   [クエリの定義](~/ef6/modeling/designer/advanced/defining-query.md)
        -   [コード生成テンプレート](~/ef6/modeling/designer/codegen/index.md)
        -   [ObjectContext に戻しています](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)
-   モデルを使用します。
    -   [DbContext の操作](~/ef6/fundamentals/working-with-dbcontext.md)
    -   [クエリを実行/検索エンティティ](~/ef6/querying/index.md)
    -   [リレーションシップの使用](~/ef6/fundamentals/relationships.md)
    -   [関連エンティティを読み込む](~/ef6/querying/related-data.md)
    -   [ローカル データの使用](~/ef6/querying/local-data.md)
    -   [N 層アプリケーション](~/ef6/fundamentals/disconnected-entities/index.md)
    -   [生 SQL クエリ](~/ef6/querying/raw-sql.md)
    -   [オプティミスティック同時実行制御パターン](~/ef6/saving/concurrency.md)
    -   [プロキシの操作](~/ef6/fundamentals/proxies.md)
    -   [自動の変更を検出します。](~/ef6/saving/change-tracking/auto-detect-changes.md)
    -   [追跡なしのクエリ](~/ef6/querying/no-tracking.md)
    -   [Load メソッド](~/ef6/querying/load-method.md)
    -   [追加またはアタッチし、エンティティの状態](~/ef6/saving/change-tracking/entity-state.md)
    -   [プロパティ値の使用](~/ef6/saving/change-tracking/property-values.md)
    -   [WPF (Windows Presentation Foundation) でのデータ バインディング](~/ef6/fundamentals/databinding/wpf.md)
    -   [WinForms (Windows フォーム) でのデータ バインディング](~/ef6/fundamentals/databinding/winforms.md)

## <a name="ef-431"></a>EF 4.3.1
4.3.1 EF ランタイムは、NuGet に EF 4.3.0 の直後後の 2012 年 2 月にリリースされました。
この修正プログラムのリリースでは、EF 4.3 リリースにいくつかのバグ修正が含まれているし、Visual Studio 2012 で EF 4.3 を使用して顧客のより優れた LocalDB のサポートが導入されました。

当社で EF 4.3.1 リリース用に具体的にはコンテンツの一覧を次に示します、EF 4.1 に指定されたコンテンツのほとんどはまだ適用 EF 4.3 にも。

-   [EF 4.3.1 リリース ブログ投稿](http://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4.3
4.3.0 EF ランタイムが NuGet では、2012 年 2 月のリリースします。
このリリースには、Code First モデルの進化に伴って変更増分する Code First によって作成されたデータベースを許可する新しい Code First Migrations の機能が含まれています。

具体的には、EF 4.3 リリースをまとめてコンテンツの一覧を次に示します、EF 4.1 に指定されたコンテンツのほとんどはまだに適用 EF 4.3 も。
-   [EF 4.3 リリースの投稿](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [EF 4.3 コード ベースの移行チュートリアル](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [EF 4.3 の自動移行のチュートリアル](http://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4.2
EF 4.2.0 ランタイムは、NuGet を 2011 年 11 月にリリースされました。
このリリースでは EF 4.1.1 のバグ修正を解放します。
このリリースにのみ含まれているため、修正プログラム リリースのバグ修正が EF 4.1.2 されている可能性がありますが、4.1.x で使用した修正プログラムのバージョン番号を解放し、採用ベースの日付から移動できるようにするため、4.2 に移動することにしました、[セマンティック Versionsing](https://semver.org)セマンティック バージョン管理の標準です。

具体的には、EF 4.2 リリースをまとめてコンテンツの一覧を次に示します、EF 4.1 の指定されたコンテンツにも該当 EF 4.2 もします。

-   [EF 4.2 のリリースの投稿](http://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [コードの最初のチュートリアル](http://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [モデルとデータベースの最初のチュートリアル](http://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
EF 4.1.10715 ランタイムは、NuGet を 2011 年 7 月にリリースされました。
バグ修正に加えてこの修正プログラムのリリースには、デザイン時の Code First モデルを操作するツールを容易にできるようにいくつかのコンポーネントが導入されました。
これらのコンポーネントは、Code First Migrations を (EF 4.3 に含まれる) と EF Power Tools で使用されます。

わかります奇妙なバージョンが、パッケージの 4.1.10715 を番号します。
日付ベースのパッチ バージョンを使用して、前に、採用することにしました使いました[セマンティック バージョニング](https://semver.org)します。
EF 4.1 更新プログラム 1 (または EF 4.1.1) として、このバージョンと考えてください。

4.1.1 をまとめてコンテンツの一覧を次に示しますリリース。

-   [EF 4.1.1 リリースの投稿](http://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4.1
4.1.10331 EF ランタイムが最初の NuGet で 2011 年 4 月に発行されます。
このリリースには、簡略化された DbContext API や Code First ワークフローが含まれています。

奇妙なバージョン番号が表示されます、4.1.10331 4.1 をされている本当にする必要があります。 さらに、4.1.10311 がバージョン 4.1.0-rc をされている必要があります ("rc"は 'release candidate')。
日付ベースのパッチ バージョンを使用して、前に、採用することにしました使いました[セマンティック バージョニング](https://semver.org)します。

4.1 のリリースをまとめてコンテンツの一覧を示します。 その大部分は、Entity Framework の以降のリリースにも適用されます。

-   [EF 4.1 のリリースの投稿](http://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [コードの最初のチュートリアル](http://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [モデルとデータベースの最初のチュートリアル](http://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure フェデレーションと Entity Framework](http://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4.0
このリリースは、2010 年 4 月の .NET Framework 4 および Visual Studio 2010 に含まれています。
このリリースで重要な新機能には、POCO サポート、外部キーのマッピング、遅延読み込み、テストの容易性の改善、カスタマイズ可能なコード生成、モデルの最初のワークフローが含まれています。

Entity Framework の 2 番目のリリースがでしたは、EF 4 に同梱されている .NET Framework のバージョンとを連携させるという名前でした。
このリリースでは、したら、私たちは NuGet で Entity Framework を使用できるようにを起動して、.NET Framework のバージョンに関連付けられていますが不要になったために、セマンティック バージョニングを採用します。

いくつかそれ以降のバージョンの .NET Framework が含まれる EF ビットの大幅な更新を付属することに注意してください。
実際には、EF 5.0 の新機能の多くは、これらのビットの機能強化として実装されました。
ただし、すべての新しいバージョンから成る中に、EF 4.0 runtime と .NET Framework の一部である EF のビットを参照する続行 EF のバージョン管理を合理化するために、 [EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)します。         

## <a name="ef-35"></a>EF 3.5
初期のバージョンの Entity Framework は、.NET 3.5 Service Pack 1 と Visual Studio 2008 SP1、2008 年 8 月のリリースに含まれていました。
このリリースでは、Database First ワークフローを使用して基本的な O/RM のサポートが用意されています。
