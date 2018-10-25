---
title: Entity Framework 6 と Entity Framework Core を比較する
description: Entity Framework 6 と Entity Framework Core のどちらを選ぶか判断する方法についてガイダンスを示します。
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 0f9f0d4708fa283855eddf2cfc231b37356e413e
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022351"
---
# <a name="compare-ef-core--ef6"></a>EF Core と EF6 を比較する

Entity Framework は、.NET 用のオブジェクト リレーショナル マッパー (O/RM) です。 この記事では、Entity Framework 6 と Entity Framework Core という 2 つのバージョンを比較します。

## <a name="entity-framework-6"></a>Entity Framework 6

Entity Framework 6 (EF6) は、実証済みのデータ アクセス テクノロジです。 これは 2008 年に初めて、.NET Framework 3.5 SP1 と Visual Studio 2008 SP1 の一部としてリリースされました。 4.1 リリース以降は、[EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet パッケージとして出荷されています。 EF6 は .NET Framework 4.x 上で実行されます。これは、EF6 が Windows 上でしか実行されないことを意味します。 

EF6 は製品として今後もサポートされます。バグ修正や細かな改善も提供されます。

## <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) は、2016 年に最初にリリースされた EF6 の完全な書き換え版です。 これは Nuget パッケージで出荷されます。主要なものとしては [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/) があります。 EF Core は、.NET Core または .NET Framework 上で実行可能なクロス プラットフォーム製品です。

EF Core は EF6 のような開発者エクスペリエンスを提供するように設計されました。 最上位レベルの API のほとんどは同じままなので、EF6 を使用していた開発者にとって EF Core は使い慣れたものと感じるでしょう。

## <a name="feature-comparison"></a>機能の比較

EF Core は、EF6 には実装されない新機能を備えています ([代替キー](xref:core/modeling/alternate-keys)、[バッチ更新プログラム](xref:core/what-is-new/ef-core-1.0#relational-batching-of-statements)、[LINQ クエリでのクライアント/データベース評価の混在](xref:core/querying/client-eval)など)。 しかし、EF Core は新しいコード ベースであるため、EF6 に含まれている機能のうち一部がありません。

次の表で、EF Core と EF6 で使用できる機能を比較します。 これは大まかな比較であるため、すべての機能を一覧しているわけではありません。また、同じ機能に関する各種 EF バージョンでの差異についても説明していません。

EF Core 列には、この機能が初めて登場した製品のバージョンが示されています。

### <a name="creating-a-model"></a>モデルの作成

| **機能**                                           | **EF 6** | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 基本クラス マッピング                                   | [はい]      | 1                                   |
| パラメーターを指定するコンストラクター                          |          | 2.1                                   |
| プロパティ値の変換                            |          | 2.1                                   |
| キーなしでマップされた型 (クエリ型)               |          | 2.1                                   |
| 規約                                           | [はい]      | 1                                   |
| カスタム規約                                    | [はい]      | 1.0 (一部)                         |
| データの注釈                                      | [はい]      | 1                                   |
| Fluent API                                            | [はい]      | 1                                   |
| 継承: Table-Per-Hierarchy (TPH)                | [はい]      | 1                                   |
| 継承: Table-Per-Type (TPT)                     | [はい]      |                                       |
| 継承: Table-Per-Concrete-Class (TPC)           | [はい]      |                                       |
| シャドウ状態プロパティ                               |          | 1                                   |
| 代替キー                                        |          | 1                                   |
| 結合エンティティなしの多対多                      | [はい]      |                                       |
| キーの生成: データベース                              | [はい]      | 1                                   |
| キーの生成: クライアント                                |          | 1                                   |
| 複合/所有型                                   | [はい]      | 2.0                                   |
| 空間データ                                          | [はい]      |                                       |
| モデルのグラフィカルな視覚エフェクト                      | [はい]      |                                       |
| グラフィカルなモデル エディター                                | [はい]      |                                       |
| モデルの形式: コード                                    | [はい]      | 1                                   |
| モデルの形式: EDMX (XML)                              | [はい]      |                                       |
| データベースからモデルを作成: コマンドライン              | [はい]      | 1                                   |
| データベースからモデルを作成: VS ウィザード                 | [はい]      |                                       |
| データベースからモデルを更新                            | Partial  |                                       |
| グローバル クエリ フィルター                                  |          | 2.0                                   |
| テーブル分割                                       | [はい]      | 2.0                                   |
| エンティティ分割                                      | [はい]      |                                       |
| データベース スカラー関数マッピング                      | 悪い     | 2.0                                   |
| フィールド マッピング                                         |          | 1.1                                   |

### <a name="querying-data"></a>データのクエリ

| **機能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ クエリ                                          | [はい]      | 1.0 (複雑なクエリで進行中) |
| 読み取り可能な SQL の生成                                | 悪い     | 1                                   |
| 混合クライアント/サーバーの評価                        |          | 1                                   |
| GroupBy 変換                                   | [はい]      | 2.1                                   |
| 関連データの読み込み: 一括                           | [はい]      | 1                                   |
| 関連データの読み込み: 派生型の一括読み込み |          | 2.1                                   |
| 関連データの読み込み: 遅延                            | [はい]      | 2.1                                   |
| 関連データの読み込み: 明示                        | [はい]      | 1.1                                   |
| 生 SQL クエリ: エンティティ型                         | [はい]      | 1                                   |
| 生 SQL クエリ: エンティティ型以外 (クエリ型)       | [はい]      | 2.1                                   |
| 生 SQL クエリ: LINQ を使用して作成                  |          | 1                                   |
| 明示的にコンパイルされたクエリ                           | 悪い     | 2.0                                   |
| テキスト ベースのクエリ言語 (Entity SQL)                | [はい]      |                                       |

### <a name="saving-data"></a>データの保存

| **機能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 変更の追跡: スナップショット                             | [はい]      | 1                                   |
| 変更の追跡: 通知                         | [はい]      | 1                                   |
| 変更の追跡: プロキシ                              | [はい]      |                                       |
| 追跡対象の状態へのアクセス                               | [はい]      | 1                                   |
| オプティミスティック コンカレンシー                                | [はい]      | 1                                   |
| トランザクション                                          | [はい]      | 1                                   |
| ステートメントのバッチ処理                                |          | 1                                   |
| ストアド プロシージャ マッピング                              | [はい]      |                                       |
| 切断されたグラフの下位 API                     | 悪い     | 1                                   |
| 切断されたグラフのエンド ツー エンド                         |          | 1.0 (一部)                         |

### <a name="other-features"></a>その他の機能

| **機能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| 移行                                            | [はい]      | 1                                   |
| データベース作成/削除 API                       | [はい]      | 1                                   |
| シード データ                                             | [はい]      | 2.1                                   |
| 接続の復元性                                 | [はい]      | 1.1                                   |
| ライフサイクル フック (イベント、インターセプション)                | [はい]      |                                       |
| 単純なログ記録 (Database.Log)                         | [はい]      |                                       |
| DbContext プール                                     |          | 2.0                                   |

### <a name="database-providers"></a>データベース プロバイダー

| **機能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | [はい]      | 1                                   |
| MySQL                                                 | [はい]      | 1                                   |
| PostgreSQL                                            | [はい]      | 1                                   |
| Oracle                                                | [はい]      | 1.0 <sup>(1)</sup>                    |
| SQLite                                                | [はい]      | 1                                   |
| SQL Server Compact                                    | [はい]      | 1.0 <sup>(2)</sup>                    |
| DB2                                                   | [はい]      | 1                                   |
| Firebird                                              | [はい]      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(2)</sup>                    |
| インメモリ (テスト用)                               |          | 1                                   |

<sup>1</sup> 現在、Oracle に対応した有料のプロバイダーがご利用になれます。 Oracle 公式の無料のプロバイダーについては現在対応を進めています。

<sup>2</sup> SQL Server Compact および Jet プロバイダーは .NET Framework (.NET Core ではない) だけに対応しています。

### <a name="net-implementations"></a>.NET 実装

| **機能**                                           | **EF6**  | **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| .NET framework (コンソール、WinForms、WPF、ASP.NET)      | [はい]      | 1                                   |
| .NET Core (コンソール、ASP.NET Core)                     |          | 1                                   |
| Mono & Xamarin                                        |          | 1.0 (進行中)                     |
| UWP                                                   |          | 1.0 (進行中)                     |

## <a name="guidance-for-new-applications"></a>新しいアプリケーションのガイダンス

次の条件の両方に該当する場合、新しいアプリケーションには EF Core の使用を検討してください。
* アプリでは .NET Core の機能が必要とされています。 詳細については、「[サーバー アプリ用 .NET Core と .NET Framework の選択](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)」を参照してください。
* EF Core では、そのアプリに必要な機能がすべてサポートされています。 必要な機能が不足している場合は、[EF Core ロードマップ](xref:core/what-is-new/roadmap)を調べて、今後その機能がサポートされる予定があるかどうかを確認します。 

次の条件の両方に該当する場合、EF 6 の使用を検討してください。
* アプリは Windows と .NET Framework 4.0 以降で実行されます。
* EF6 では、そのアプリに必要な機能がすべてサポートされています。

## <a name="guidance-for-existing-ef6-applications"></a>既存の EF6 アプリケーションに関するガイダンス

EF Core には根本的な変更が加えられているため、変更するやむを得ない理由がない限り、EF6 アプリケーションの EF Core への移行はお勧めできません。 新機能を利用するために EF Core に移行する場合には、必ず制限事項を確認してください。 詳細については、「[EF6 から EF Core へ移植](porting/index.md)」をご覧ください。 **EF6 から EF Core への移行は、アップグレードというよりは移植です。** 

## <a name="next-steps"></a>次の手順

詳細については、次のドキュメントを参照してください。
* <xref:core/index>
* <xref:ef6/index>
