---
title: "EF Core 1.0 の新機能 - EF Core"
author: divega
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: e5b9e57a01ff302b1d7bd0fc5419aa5b8213865e
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="features-included-in-ef-core-10"></a>EF Core 1.0 に含まれる機能

## <a name="platforms"></a>プラットフォーム
### <a name="net-framework-451"></a>.NET Framework 4.5.1
コンソール、WPF、WinForms、ASP.NET 4 などが含まれます。
### <a name="net-standard-13"></a>.NET Standard 1.3
.NET Framework と、Windows、OSX および Linux での .NET Core の両方を対象にした ASP.NET Core が含まれています。

## <a name="modelling"></a>モデリング
### <a name="basic-modelling"></a>基本モデリング
共通のスカラー型 (`int`、`string` など) の get/set プロパティを持つ POCO エンティティに基づいています。
### <a name="relationships-and-navigation-properties"></a>リレーションシップとナビゲーション プロパティ
外部キーに基づく一対多、一対ゼロまたは一対一のリレーションシップをモデルに指定できます。 単純なコレクションまたは参照型のナビゲーション プロパティを、これらのリレーションシップに関連付けることができます。
### <a name="built-in-conventions"></a>組み込みの規約
組み込みの規約は、エンティティ クラスの構造に基づいて、初期モデルを作成します。
### <a name="fluent-api"></a>Fluent API
使用するコンテキストの `OnModelCreating` メソッドを上書きして、規約によって検出されたモデルをさらに構成できます。
### <a name="data-annotations"></a>データの注釈
エンティティのクラスやプロパティに追加できる属性で、EF モデルに影響を与えます (たとえば [必須] を追加すると、プロパティが必須であることが EF に通知されます)。
### <a name="relational-table-mapping"></a>リレーショナル テーブルのマッピング
エンティティをテーブルや列にマップできます。
### <a name="key-value-generation"></a>キー値の生成
クライアント側での生成と、データベースでの生成が含まれます。
### <a name="database-generated-values"></a>データベースで生成される値
データベースでの挿入 (既定値) や更新 (計算列) によって値を生成できます。
### <a name="sequences-in-sql-server"></a>SQL Server 内のシーケンス
モデルにシーケンス オブジェクトを定義できます。
### <a name="unique-constraints"></a>UNIQUE 制約
代替キーを定義し、そのキーを対象とするリレーションシップを定義する機能を利用できます。
### <a name="indexes"></a>インデックス
モデルにインデックスを定義すると、インデックスが自動的にデータベースに導入されます。 一意なインデックスもサポートされています。
### <a name="shadow-state-properties"></a>シャドウ状態プロパティ
.NET クラスで宣言されておらず、このクラスに格納もされていないが、EF Core で追跡および更新できるプロパティをモデルに定義できます。 オブジェクト内の外部キー プロパティを公開することが好ましくない場合に、外部キー プロパティの用途でよく使用されます。
### <a name="table-per-hierarchy-inheritance-pattern"></a>Table-per-Hierarchy 継承パターン
データベース内の特定レコードのエンティティの種類を識別する識別子の列を使用して、継承階層内のエンティティを 1 つのテーブルに保存できます。
### <a name="model-validation"></a>モデルの検証
モデル内の無効なパターンを検出し、有用なエラー メッセージを示します。

## <a name="change-tracking"></a>Change tracking
### <a name="snapshot-change-tracking"></a>スナップショットによる変更の追跡
現在の状態を元の状態のコピー (スナップショット) と比較することで、エンティティの変更を自動的に検出できます。
### <a name="notification-change-tracking"></a>通知による変更の追跡
プロパティの値が変更されると、エンティティから変更トラッカーに通知できます。
### <a name="accessing-tracked-state"></a>追跡対象の状態へのアクセス
`DbContext.Entry` と `DbContext.ChangeTracker` を使用します。
### <a name="attaching-detached-entitiesgraphs"></a>デタッチされたエンティティやグラフのアタッチ
この新しい `DbContext.AttachGraph` API では、新しいまたは変更されたエンティティを保存するために、コンテキストにエンティティを再アタッチできます。

## <a name="saving-data"></a>データの保存
### <a name="basic-save-functionality"></a>基本の保存機能
エンティティ インスタンスへの変更をデータベースに保存できます。
### <a name="optimistic-concurrency"></a>オプティミスティック同時実行制御
データベースからデータがフェッチされた後、別のユーザーが行った変更が上書きされないようにします。
### <a name="async-savechanges"></a>非同期 SaveChanges
データベースで `SaveChanges` から発行されたコマンドを処理している間に他の要求を処理できるように、現在のスレッドを解放できます。
### <a name="database-transactions"></a>データベース トランザクション
`SaveChanges` が常にアトミックであることを示します。つまり、完全に成功するか、データベースが変更されないかどちらかです。 コンテキスト インスタンスなどの間でトランザクションを共有できる、トランザクションに関連した API もあります。
### <a name="relational-batching-of-statements"></a>リレーショナル: ステートメントのバッチ処理
複数の INSERT/UPDATE/DELETE コマンドを 1 度のラウンドトリップでデータベースにバッチ更新することにより、パフォーマンスを向上させます。

## <a name="query"></a>クエリ
### <a name="basic-linq-support"></a>LINQ の基本的なサポート
LINQ を使用してデータベースからデータを取得する機能を提供します。
### <a name="mixed-clientserver-evaluation"></a>混合クライアント/サーバーの評価
データベース内で評価できないためにデータをメモリに取得してから評価する必要のあるロジックをクエリに組み込むことができます。
### <a name="notracking"></a>NoTracking
コンテキストでエンティティ インスタンスへの変更を監視する必要がない場合には (結果が読み取りのみの場合など)、クエリをより速く実行できます。
### <a name="eager-loading"></a>一括読み込み
クエリ実行時にフェッチも必要な関連データを識別する `Include` および `ThenInclude` メソッドを提供します。
### <a name="async-query"></a>非同期クエリ
データベースでクエリを処理している間に他の要求を処理できるように、現在のスレッド (とそれに関連するリソース) を解放できます。
### <a name="raw-sql-queries"></a>生 SQL クエリ
生 SQL クエリを使用してデータをフェッチする `DbSet.FromSql` メソッドを提供します。 これらのクエリは、LINQ を使用して構成することもできます。

## <a name="database-schema-management"></a>データベース スキーマの管理       
### <a name="database-creationdeletion-apis"></a>データベース作成/削除 API
これらの API の大半は、データベースを移行せずにすばやく作成または削除する必要のあるテスト向けに設計されています。
### <a name="relational-database-migrations"></a>リレーショナル データベースの移行
リレーショナル データベース スキーマを、モデルの変化に合わせて長期間かけて進化させることができます。
### <a name="reverse-engineer-from-database"></a>データベースからのリバース エンジニアリング
既存のリレーショナル データベース スキーマに基づいて、EF モデルをスキャフォールディングします。

## <a name="database-providers"></a>データベース プロバイダー
### <a name="sql-server"></a>SQL Server
Microsoft SQL Server 2008 以降に接続します。
### <a name="sqlite"></a>SQLite
SQLite 3 データベースに接続します。
### <a name="in-memory"></a>In-Memory
実際のデータベースに接続せずに簡単にテストできるように設計されています。
### <a name="3rd-party-providers"></a>サード パーティ プロバイダー
他のデータベース エンジンで使用できるプロバイダーもあります。 すべてのプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../providers/index.md)」をご覧ください。
