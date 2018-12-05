---
title: データのシード処理 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 8f28dfea12461572ade8fbf3910ebd216dafb389
ms.sourcegitcommit: fa863883f1193d2118c2f9cee90808baa5e3e73e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52857430"
---
# <a name="data-seeding"></a>データのシード処理

データのシード処理は、データの初期セットでのデータベースの設定のプロセスです。

いくつかの方法を EF Core でこれを実現できます。
* モデルのシード データ
* 手動移行のカスタマイズ
* カスタムの初期化ロジック

## <a name="model-seed-data"></a>モデルのシード データ

> [!NOTE]
> これは EF Core 2.1 の新機能です。

異なり、EF Core での EF6 でデータをシード処理関連付けることができますモデル構成の一部としてエンティティ型。 EF Core し[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースのアップグレード時に適用される操作の必要性を自動的に計算できます。

> [!NOTE]
> 移行では、目的の状態にシード データを取得するは、どのような操作を実行するかを決定するときにモデルの変更のみ考慮されます。 したがって移行の外部で実行するデータに対する変更は失われるまたはエラーが発生する可能性があります。

たとえば、シード データに設定されます、`Blog`で`OnModelCreating`:

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

エンティティ型に任意のプロパティがある場合は、シャドウ状態では、値を提供する匿名のクラスを使用できます。

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

所有エンティティの型は、同様の方法でシード処理できます。

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)詳細なコンテキスト。

データがモデルに追加されたら[移行](xref:core/managing-schemas/migrations/index)変更を適用するために使用する必要があります。

> [!TIP]
> 自動化された展開の一部として移行を適用する必要がある場合は、 [SQL スクリプトを作成する](xref:core/managing-schemas/migrations/index#generate-sql-scripts)を実行する前にプレビューを表示できます。

使用する代わりに、`context.Database.EnsureCreated()`テスト データベースの例を使用するか、メモリ内プロバイダーまたは関係以外の任意のデータベースを使用して、シード データを含む新しいデータベースを作成します。 されている場合、データベースが既に存在する`EnsureCreated()`どちらも、データベース内のスキーマとシード データが更新されます。 リレーショナル データベースに対して呼び出すべきではない`EnsureCreated()`Migrations を使用する場合。

この種類のシード データは移行によって管理され、データベースに既にあるデータを更新するスクリプトは、データベースに接続しなくても生成する必要があります。 これで、いくつかの制限が課せられます。
* 主キーの値は、通常、データベースによって生成される場合でも指定する必要があります。 これは、移行の間のデータの変更を検出するために使用されます。
* 以前の主キーが何らかの方法で変更された場合、シードされたデータは削除されます。

したがってこの機能は、静的なデータ移行の外部で変更するのには想定されていませんが、データベースでは、郵便番号などの他の要素に依存しないに最も適しています。

自分のシナリオでは、次のいずれかが含まれる場合は、最後のセクションで説明されているカスタムの初期化ロジックを使用することをお勧めします。
* テスト用の一時的なデータ
* データベースの状態に依存するデータ
* キーの値を id として代替キーを使用してエンティティを含む、データベースが生成する必要があるデータ
* カスタム変換を必要とするデータ (によって処理されない[値変換](xref:core/modeling/value-conversions))、いくつかのパスワード ハッシュなど
* ASP.NET Core Id のロールとユーザーの作成などの外部 API の呼び出しを必要とするデータ

## <a name="manual-migration-customization"></a>手動移行のカスタマイズ

移行が追加されたときの変更で指定されたデータに`HasData`への呼び出しに変換される`InsertData()`、 `UpdateData()`、および`DeleteData()`します。 いくつかの制限を回避する方法の 1 つ`HasData`は、手動でこれらの呼び出しを追加するまたは[カスタム操作](xref:core/managing-schemas/migrations/operations)移行に代わりにします。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>カスタムの初期化ロジック

データのシード処理を実行する簡単で強力な方法は使用する[ `DbContext.SaveChanges()` ](xref:core/saving/index)ロジックは、メイン アプリケーションの前に実行を開始します。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> シード処理のコードは、通常のアプリの実行の一部をしないでように複数のインスタンスを実行しているし、データベース スキーマを変更する権限のあるアプリも必要になりますと同時実行の問題が発生します。

配置の制約に応じて、さまざまな方法で初期化コードを実行できます。
* 初期化のアプリのローカル実行
* 初期化ルーチンを呼び出すと無効化または初期化アプリの削除は、メイン アプリケーションの初期化のアプリを展開します。

これを使用して、通常は自動化できます[発行プロファイル](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles)します。
