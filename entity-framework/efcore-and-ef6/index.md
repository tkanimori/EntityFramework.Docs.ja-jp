---
title: EF6 と EF Core を比較する
description: EF6 と EF Core から選択する方法に関するガイダンス。
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: e28c7d0299e5089f56fb0795d00917cfc30f5cf1
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888149"
---
# <a name="compare-ef-core--ef6"></a>EF Core と EF6 を比較する

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](../core/index.md)) は、.NET 用の最新のオブジェクト データベース マッパーです。 LINQ クエリ、変更の追跡、更新、スキーマの移行がサポートされています。

EF Core は、[データベース プロバイダーのプラグイン モデル](../core/providers/index.md)を使用して、SQL Server または SQL Azure、SQLite、Azure Cosmos DB、MySQL、PostgreSQL、その他多くのデータベースと連携します。

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](../ef6/index.md)) は、.NET Framework 向けに設計されたオブジェクト リレーショナル マッパーですが、.NET Core をサポートしています。 EF6 は安定したサポートされている製品ですが、現在は積極的に開発されていません。

## <a name="feature-comparison"></a>機能の比較

EF Core には、EF6 に実装されない新機能が用意されています。 しかし、現在、すべての EF6 機能が EF Core に実装されているわけではありません。

次の表で、EF Core と EF6 で使用できる機能を比較します。 これは大まかな比較であるため、すべての機能を一覧しているわけではありません。また、同じ機能に関する各種 EF バージョンでの差異についても説明していません。

EF Core 列には、この機能が初めて登場した製品のバージョンが示されています。

### <a name="creating-a-model"></a>モデルの作成

| **機能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 基本クラス マッピング                                   | はい      | 1                                   |
| パラメーターを指定するコンストラクター                          |          | 2.1                                   |
| プロパティ値の変換                            |          | 2.1                                   |
| キーなしでマップされた型                             |          | 2.1                                   |
| 規約                                           | はい      | 1                                   |
| カスタム規約                                    | はい      | 1.0 (一部、[#214](https://github.com/dotnet/efcore/issues/214)) |
| データの注釈                                      | はい      | 1                                   |
| Fluent API                                            | はい      | 1                                   |
| 継承:Table-Per-Hierarchy (TPH)                | はい      | 1                                   |
| 継承:Table-Per-Type (TPT)                     | はい      | 5\.0 で予定 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| 継承:Table‐Per‐Concrete‐Class (TPC)           | はい      | 5\.0 で拡張 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| シャドウ状態プロパティ                               |          | 1                                   |
| 代替キー                                        |          | 1                                   |
| 多対多ナビゲーション                              | はい      | 5\.0 で予定 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| 結合エンティティなしの多対多                      | はい      | バックログ ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| キーの生成:データベース                              | はい      | 1                                   |
| キーの生成:クライアント                                |          | 1                                   |
| 複合/所有型                                   | はい      | 2.0                                   |
| 空間データ                                          | はい      | 2.2                                   |
| モデルの形式:コード                                    | はい      | 1                                   |
| データベースからモデルを作成:コマンド ライン              | はい      | 1                                   |
| データベースからモデルを更新                            | Partial  | バックログ ([#831](https://github.com/dotnet/efcore/issues/831)) |
| グローバル クエリ フィルター                                  |          | 2.0                                   |
| テーブル分割                                       | はい      | 2.0                                   |
| エンティティ分割                                      | はい      | 5\.0 で拡張 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| データベース スカラー関数マッピング                      | 不十分     | 2.0                                   |
| フィールド マッピング                                         |          | 1.1                                   |
| Null 許容参照型 (C# 8.0)                     |          | 3.0                                   |
| モデルのグラフィカルな視覚エフェクト                      | はい      | サポートは計画されていません <sup>(2)</sup>     |
| グラフィカルなモデル エディター                                | はい      | サポートは計画されていません <sup>(2)</sup>     |
| モデルの形式:EDMX (XML)                              | はい      | サポートは計画されていません <sup>(2)</sup>     |
| データベースからモデルを作成:ウィザード                 | はい      | サポートは計画されていません <sup>(2)</sup>     |

### <a name="querying-data"></a>データのクエリ

| **機能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ クエリ                                          | はい      | 1                                   |
| 読み取り可能な SQL の生成                                | 悪い     | 1                                   |
| GroupBy 変換                                   | はい      | 2.1                                   |
| 関連データの読み込み:集中評価                           | はい      | 1                                   |
| 関連データの読み込み:派生型の一括読み込み |          | 2.1                                   |
| 関連データの読み込み:遅延                            | はい      | 2.1                                   |
| 関連データの読み込み:Explicit                        | はい      | 1.1                                   |
| 生 SQL クエリ:エンティティ型                         | はい      | 1                                   |
| 生 SQL クエリ:キーなしエンティティ型                 | はい      | 2.1                                   |
| 生 SQL クエリ:LINQ による作成                  |          | 1                                   |
| 明示的にコンパイルされたクエリ                           | 悪い     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| テキスト ベースのクエリ言語 (Entity SQL)                | はい      | サポートは計画されていません <sup>(2)</sup>     |

### <a name="saving-data"></a>データの保存

| **機能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 変更追跡:スナップショット                             | はい      | 1                                   |
| 変更追跡:通知                         | はい      | 1                                   |
| 変更追跡:プロキシ                              | はい      | 5\.0 でマージ ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| 追跡対象の状態へのアクセス                               | はい      | 1                                   |
| オプティミスティック コンカレンシー                                | はい      | 1                                   |
| トランザクション                                          | はい      | 1                                   |
| ステートメントのバッチ処理                                |          | 1                                   |
| ストアド プロシージャ マッピング                              | はい      | バックログ ([#245](https://github.com/dotnet/efcore/issues/245)) |
| 切断されたグラフの下位 API                     | 悪い     | 1                                   |
| 切断されたグラフのエンド ツー エンド                         |          | 1.0 (一部、[#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>その他の機能

| **機能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 移行                                            | はい      | 1                                   |
| データベース作成/削除 API                       | はい      | 1                                   |
| シード データ                                             | はい      | 2.1                                   |
| 接続の復元性                                 | はい      | 1.1                                   |
| インターセプター                                          | はい      | 3.0                                   |
| イベント                                                | はい      | 3.0 (一部、[#626](https://github.com/dotnet/efcore/issues/626)) |
| 単純なログ記録 (Database.Log)                         | はい      | 5\.0 でマージ ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| DbContext プール                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>データベース プロバイダー <sup>(3)</sup>

| **機能**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | はい      | 1                                   |
| MySQL                                                 | はい      | 1                                   |
| PostgreSQL                                            | はい      | 1                                   |
| Oracle                                                | はい      | 1                                   |
| SQLite                                                | はい      | 1                                   |
| SQL Server Compact                                    | はい      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | はい      | 1                                   |
| Firebird                                              | はい      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| インメモリ (テスト用)                               |          | 1                                   |

<sup>1</sup> 拡張の目標は、指定したリリースで達成される可能性は低いです。 しかし、うまく進んだ場合は、それらを取り込むようにします。

<sup>2</sup> 一部の EF6 機能は EF Core に実装されません。 これらの機能は、EF6 の基になる Entity Data Model (EDM) に依存しているか、または投資収益率が比較的低い、複雑な機能であるかのいずれかです。 Microsoft は常にフィードバックをお待ちしていますが、EF Core では EF6 でできない多くのことを可能にする一方、EF6 のすべての機能をサポートすることは、EF Core では逆に実行不可能です。

<sup>3</sup> サード パーティによって実装されている EF Core データベース プロバイダーは、EF Core の新しいメジャー バージョンへの更新に遅れが生じる可能性があります。 詳しくは、「[データベース プロバイダー](../core/providers/index.md)」をご覧ください。

<sup>4</sup> SQL Server Compact および Jet プロバイダーは、.NET Framework (.NET Core ではない) のみに対応しています。

### <a name="supported-platforms"></a>サポートされているプラットフォーム

EF Core 3.1 は .NET Standard 2.0 を使用して、.NET Core と .NET Framework で実行されます。 ただし、EF Core 5.0 は .NET Framework では実行されません。 詳細については、[プラットフォーム](../core/platforms/index.md)に関するページを参照してください。

EF 6.4 は、複数バージョン対応によって .NET Core と .NET Framework で実行されます。

## <a name="guidance-for-new-applications"></a>新しいアプリケーションのガイダンス

アプリが [.NET Framework でのみサポートされている](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)ものを必要な場合を除き、すべての新しいアプリケーションに対して .NET Core で EF Core を使用します。

## <a name="guidance-for-existing-ef6-applications"></a>既存の EF6 アプリケーションに関するガイダンス

EF Core は EF6 のドロップイン置換ではありません。 EF6 から EF Core に移行すると、アプリケーションを変更する必要がある可能性が高いです。

EF6 アプリを .NET Core に移行する場合
* データ アクセス コードが安定しており、進化したり新しい機能が必要になったりする可能性が低い場合は、EF6 を継続して使用してください。
* データ アクセス コードが進化している場合、またはアプリが EF Core でのみ使用できる新機能を必要とする場合に、EF Core に移植します。
* EF Core への移植は、パフォーマンスのために行われることも多いです。 しかし、すべてのシナリオで速くなるわけではないため、最初にプロファイルを実行してください。

詳細については、「[Porting from EF6 to EF Core (EF6 から EF Core への移植)](porting/index.md)」をご覧ください。
