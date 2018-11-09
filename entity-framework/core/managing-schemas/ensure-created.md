---
title: 作成し、Drop Api - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/10/2017
ms.openlocfilehash: 336f6fd655603a2474a58dfef377e121d9b04c3a
ms.sourcegitcommit: a088421ecac4f5dc5213208170490181ae2f5f0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285640"
---
# <a name="create-and-drop-apis"></a>作成し、Drop Api

EnsureCreated と EnsureDeleted メソッドに軽量な代替手段を提供[移行](migrations/index.md)データベース スキーマを管理するためです。 これは、機能は、データが一時的であり、スキーマが変更されたときに削除することができる場合のシナリオで便利です。 プロトタイプ作成、テストでは、またはのローカル キャッシュたとえば中です。

一部のプロバイダー (特に非リレーショナル) は、移行でサポートされていません。 これらの場合は、EnsureCreated と、データベース スキーマを初期化するために最も簡単な方法では多くの場合です。

> [!WARNING]
> EnsureCreated と移行は、うまく連携機能しません。 移行を使用している場合は、スキーマを初期化するために EnsureCreated を使用しないでください。

EnsureCreated からの移行への移行は、シームレスなエクスペリエンスではありません。 これを実現する simpelest 方法、データベースを削除し、Migrations を使用して再作成です。 Migrations を使用して、将来を予測する場合は、EnsureCreated を使用する代わりに移行から始めることをお勧めします。

## <a name="ensuredeleted"></a>EnsureDeleted

存在する場合、EnsureDeleted メソッドは、データベースを削除します。 適切なアクセス許可を持っていない場合は、例外がスローされます。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

存在しないし、データベース スキーマを初期化する場合、EnsureCreated は、データベースを作成します。 任意のテーブルが存在しない場合 (他の DbContext クラスでテーブルを含む)、スキーマはを初期化しません。

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> これらのメソッドの非同期バージョンも使用できます。

## <a name="sql-script"></a>SQL スクリプト

EnsureCreated で使用される SQL を取得するには、GenerateCreateScript メソッドを使用できます。

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>複数の DbContext クラス

EnsureCreated では、テーブルがデータベースに存在しない場合にのみ機能します。 必要に応じて、して初期化するのには、スキーマが必要なかどうかは、独自のチェックを記述し、基になる IRelationalDatabaseCreator サービスを使用してスキーマを初期化します。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
