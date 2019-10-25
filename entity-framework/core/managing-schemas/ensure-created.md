---
title: Api の作成と削除-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811789"
---
# <a name="create-and-drop-apis"></a>Create API と Drop API

EnsureCreated メソッドと EnsureDeleted メソッドは、データベーススキーマを管理するための[移行](migrations/index.md)のための軽量な代替手段を提供します。 これらのメソッドは、データが一時的なもので、スキーマが変更されたときに削除できる場合に便利です。 たとえば、プロトタイプの場合、テストの場合は、ローカルキャッシュの場合はです。

一部のプロバイダー (特に非リレーショナル) は、移行をサポートしていません。 これらのプロバイダーでは、多くの場合、EnsureCreated はデータベーススキーマを初期化する最も簡単な方法です。

> [!WARNING]
> EnsureCreated と移行はうまく連携しません。 移行を使用している場合は、EnsureCreated を使用してスキーマを初期化しないでください。

EnsureCreated から移行への移行は、シームレスなエクスペリエンスではありません。 最も簡単な方法は、データベースを削除し、移行を使用してデータベースを再作成することです。 今後移行を使用する予定がある場合は、EnsureCreated を使用する代わりに、移行を開始することをお勧めします。

## <a name="ensuredeleted"></a>EnsureDeleted

EnsureDeleted メソッドは、データベースが存在する場合、そのデータベースを削除します。 適切なアクセス許可がない場合は、例外がスローされます。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

データベースが存在しない場合は、EnsureCreated によって作成され、データベーススキーマが初期化されます。 テーブルが存在する場合 (別の DbContext クラスのテーブルを含む)、スキーマは初期化されません。

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> これらのメソッドの非同期バージョンも使用できます。

## <a name="sql-script"></a>SQL スクリプト

EnsureCreated によって使用される SQL を取得するには、GenerateCreateScript メソッドを使用します。

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>複数の DbContext クラス

EnsureCreated は、データベース内にテーブルが存在しない場合にのみ機能します。 必要に応じて、独自のチェックを記述してスキーマを初期化する必要があるかどうかを確認し、基になる IRelationalDatabaseCreator サービスを使用してスキーマを初期化できます。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
