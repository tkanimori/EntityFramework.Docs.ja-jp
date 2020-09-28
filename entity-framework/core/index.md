---
title: Entity Framework Core の概要 - EF Core
description: Entity Framework Core の一般的な概要
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: 011900b55bc941d6ae6a7ac8aca6001713088c69
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210381"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。

EF Core は、次のようなオブジェクト リレーショナル マッパー (O/RM) として機能します。

* .NET 開発者が .NET オブジェクトを使用してデータベースを操作できるようにする。
* 通常記述しなければならないデータアクセス コードの多くを不要にする。

EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](xref:core/providers/index)」(データベース プロバイダー) を参照してください。

## <a name="the-model"></a>Model

EF Core では、データ アクセスはモデルを利用して実行されます。 モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。 このコンテキスト オブジェクトにより、データのクエリと保存が可能になります。 詳しくは、「[モデルの作成](xref:core/modeling/index)」をご覧ください。

EF は、次のモデル開発アプローチをサポートしています。

* 既存のデータベースからモデルを生成する。
* データベースに合わせてモデルのコードを手動で書く。
* モデルが作成されたら、[EF の移行](xref:core/managing-schemas/migrations/index) を使用して、モデルからデータベースを作成します。 移行により、モデルの変更に応じてデータベースを進化させることができます。

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>クエリ実行

エンティティ クラスのインスタンスは、[統合言語クエリ (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/) を利用し、データベースから取得されます。 詳しくは、「[データのクエリ](xref:core/querying/index)」をご覧ください。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>データの保存

データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。 詳細については、「[Saving Data](xref:core/saving/index)」 (データの保存) を参照してください。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>EF ORM の考慮事項

EF Core は多くのプログラミングの詳細を抽象化するのに適していますが、運用アプリでよくある落とし穴を回避するために、どの ORM にも適用できるいくつかのベスト プラクティスがあります。

 - 高パフォーマンスの運用アプリでデータを設計、デバッグ、プロファイル、移行するには、基になるデータベース サーバーの中級レベル以上の知識が不可欠です。 たとえば、主キーと外部キー、制約、インデックス、正規化、DML および DDL のステートメント、データ型、プロファイルなどの知識です。
- 機能と統合のテスト: 運用環境は、できるだけ次が行われるようにレプリケートすることが重要です。
  - 特定のバージョンまたはエディションのデータベース サーバーを使用している場合にのみ表示される、アプリのイシューを検索する。
  - EF Core およびその他の依存関係をアップグレードするときに、破壊的変更をキャッチする。 たとえば、ASP.NET Core、OData、Automapper などのフレームワークの追加またはアップグレードです。 これらの依存関係は、予期しない形で EF Core に影響を与える可能性があります。
- 代表的な負荷によるパフォーマンスとストレス テスト。 一部の機能は、そのまま使用しても、あまりスケーリングされません。 たとえば、複数コレクションのインクルード、遅延読み込みの頻繁な使用、非インデックス列に対する条件付きのクエリ、ストアで生成された値を使用した大規模な更新と挿入、コンカレンシー処理の欠如、大規模なモデル、不適切なキャッシュ ポリシーなどです。
- セキュリティ レビュー: たとえば、接続文字列やその他のシークレットの処理、非配置操作に対するデータベースのアクセス許可、未加工の SQL の入力検証、機密データの暗号化などです。
- ログと診断が十分であり、使用可能であることを確認します。 たとえば、適切なログ構成、クエリ タグ、Application Insights などです。
- エラー回復。 バージョンのロールバック、サーバーのフォールバック、スケールアウトと負荷分散、DoS の軽減、データ バックアップなど、一般的なエラーのシナリオの緊急時に対する準備を行います。
- アプリケーションの配置と移行。 配置中に移行がどのように適用されるかを計画します。これをアプリケーションの起動時に実行すると、コンカレンシーのイシューが発生し、通常の操作に必要なものより高いアクセス許可が必要になります。 ステージングを使用して、移行中の致命的なエラーからの回復を容易にします。 詳しくは、「[移行の適用](xref:core/managing-schemas/migrations/applying)」をご覧ください。
- 生成された移行の詳細な調査とテスト。 移行は、運用データに適用する前に十分にテストする必要があります。 テーブルに運用データが格納されると、スキーマの構造と列の型を簡単に変更することはできません。 たとえば、SQL Server では、`nvarchar(max)` と `decimal(18, 2)` が文字列と 10 進数のプロパティにマップされた列に最適な型になることはまれですが、EF では特定のシナリオについての知識がないために、既定値として使用されます。

## <a name="next-steps"></a>次の手順

入門チュートリアルについては、「[Entity Framework Core の概要](xref:core/get-started/index)」を参照してください。
