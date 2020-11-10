---
title: データシード処理-EF Core
description: データシード処理を使用した、Entity Framework Core を使用したデータベースへの初期データセットの読み込み
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: fefa6232496cd250d52a436971251f59af09f5c6
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429677"
---
# <a name="data-seeding"></a>データのシード処理

データシード処理とは、データベースに初期データセットを設定するプロセスのことです。

EF Core では、次のいくつかの方法で実現できます。

* モデルシードデータ
* 手動による移行のカスタマイズ
* カスタム初期化ロジック

## <a name="model-seed-data"></a>モデルシードデータ

EF6 とは異なり、EF Core では、シード処理データをモデル構成の一部としてエンティティ型に関連付けることができます。 その後、EF Core の [移行](xref:core/managing-schemas/migrations/index) では、データベースを新しいバージョンのモデルにアップグレードするときに、挿入、更新、または削除操作をどのように適用する必要があるかを自動的に計算できます。

> [!NOTE]
> 移行では、シードデータを目的の状態に取得するために実行する操作を決定する際に、モデルの変更のみを考慮します。 そのため、移行の外部で実行されたデータの変更が失われたり、エラーが発生したりする可能性があります。

例として、これによってののシードデータが構成され `Blog` `OnModelCreating` ます。

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

リレーションシップを持つエンティティを追加するには、外部キー値を指定する必要があります。

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

エンティティ型のプロパティが shadow 状態の場合、匿名クラスを使用して値を指定できます。

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

所有されているエンティティ型は、同様の方法でシード処理できます。

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

詳細なコンテキストについては、 [完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) を参照してください。

データがモデルに追加されたら、 [移行](xref:core/managing-schemas/migrations/index) を使用して変更を適用する必要があります。

> [!TIP]
> 自動デプロイの一部として移行を適用する必要がある場合は、実行前にプレビューできる [SQL スクリプトを作成](xref:core/managing-schemas/migrations/index#generate-sql-scripts) できます。

または、を使用して、 `context.Database.EnsureCreated()` テストデータベースなどのシードデータを含む新しいデータベースを作成したり、メモリ内のプロバイダーまたは非リレーションデータベースを使用したりすることもできます。 データベースが既に存在する場合、 `EnsureCreated()` はスキーマもデータベース内のシードデータも更新しないことに注意してください。 リレーショナルデータベースの `EnsureCreated()` 場合、移行を使用する予定がある場合は、を呼び出さないでください。

### <a name="limitations-of-model-seed-data"></a>モデルシードデータの制限事項

この種類のシードデータは、移行によって管理されます。また、データベースに既に存在するデータを更新するためのスクリプトは、データベースに接続せずに生成する必要があります。 これにはいくつかの制限があります。

* 主キーの値は、通常、データベースによって生成される場合でも指定する必要があります。 移行間のデータ変更を検出するために使用されます。
* 以前にシードされたデータは、主キーが何らかの方法で変更されると削除されます。

したがって、この機能は、移行の外部で変更されることが想定されておらず、データベース内の他のもの (郵便番号など) に依存しない静的データに対して最も役立ちます。

シナリオに以下のいずれかが含まれている場合は、前のセクションで説明したカスタム初期化ロジックを使用することをお勧めします。

* テスト用の一時データ
* データベースの状態に依存するデータ
* サイズの大きいデータ (データのシード処理は移行スナップショットでキャプチャされ、大きなデータは非常に大きなファイルになり、パフォーマンスが低下する可能性があります)。
* Id として代替キーを使用するエンティティを含む、データベースによって生成されるキー値を必要とするデータ
* 一部のパスワードのハッシュなど、カスタム変換を必要とする ( [値の変換](xref:core/modeling/value-conversions)によって処理されない) データ
* ASP.NET Core Id ロールやユーザーの作成など、外部 API の呼び出しを必要とするデータ

## <a name="manual-migration-customization"></a>手動による移行のカスタマイズ

移行を追加すると、で指定したデータに加えた変更 `HasData` が、、、およびの呼び出しに変換され `InsertData()` `UpdateData()` `DeleteData()` ます。 の制限事項を回避する方法の1つと `HasData` して、これらの呼び出し [やカスタム操作](xref:core/managing-schemas/migrations/operations) を移行に手動で追加する方法があります。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>カスタム初期化ロジック

データシード処理を実行するための簡単で強力な方法は、 [`DbContext.SaveChanges()`](xref:core/saving/index) メインアプリケーションロジックの実行が開始される前に使用することです。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> シード処理コードは、複数のインスタンスが実行されている場合は同時実行の問題が発生する可能性があり、アプリケーションにデータベーススキーマを変更するアクセス許可も必要になるため、通常のアプリの実行には含めないでください。

配置の制約に応じて、初期化コードをさまざまな方法で実行できます。

* 初期化アプリをローカルで実行する
* メインアプリで初期化アプリをデプロイし、初期化ルーチンを呼び出し、初期化アプリを無効化または削除します。

これは通常、 [発行プロファイル](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)を使用して自動化できます。
