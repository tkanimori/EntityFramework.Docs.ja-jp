---
title: カスタム移行操作-EF Core
description: Entity Framework Core を使用したデータベーススキーマ管理のためのカスタムおよび生の SQL 移行の管理
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429833"
---
# <a name="custom-migrations-operations"></a>カスタム移行操作

MigrationBuilder API を使用すると、移行中にさまざまな種類の操作を実行できますが、これは包括的なものではありません。 ただし、API を拡張して、独自の操作を定義することもできます。 API を拡張するには、メソッドを使用する方法と、 `Sql()` カスタムオブジェクトを定義する方法の2つの方法があります。 `MigrationOperation`

例として、各方法を使用してデータベースユーザーを作成する操作を実装する方法を見てみましょう。 この移行では、次のコードを記述できるようにします。

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>MigrationBuilder () の使用

カスタム操作を実装する最も簡単な方法は、を呼び出す拡張メソッドを定義することです `MigrationBuilder.Sql()` 。 次に、適切な Transact-sql を生成する例を示します。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> ステートメントが `EXEC` SQL バッチ内の最初のステートメントまたは1つのステートメントである必要がある場合は、関数を使用します。 参照されている列がテーブルに存在しない場合に発生する可能性があるべき等移行スクリプトで、パーサーエラーを回避することが必要な場合もあります。

移行で複数のデータベースプロバイダーをサポートする必要がある場合は、プロパティを使用でき `MigrationBuilder.ActiveProvider` ます。 Microsoft SQL Server と PostgreSQL の両方をサポートする例を次に示します。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

この方法は、カスタム操作が適用されるすべてのプロバイダーがわかっている場合にのみ機能します。

## <a name="using-a-migrationoperation"></a>MigrationOperation を使用する

SQL からカスタム操作を分離するには、独自のを定義してそれを表すことができ `MigrationOperation` ます。 次に、操作がプロバイダーに渡され、生成する適切な SQL を決定できるようになります。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

この方法では、拡張メソッドでこれらの操作のいずれかをに追加するだけで済み `MigrationBuilder.Operations` ます。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

この方法では、各プロバイダーがサービスでこの操作の SQL を生成する方法を知る必要があり `IMigrationsSqlGenerator` ます。 新しい操作を処理するために SQL Server のジェネレーターをオーバーライドする例を次に示します。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

既定の移行 sql ジェネレーターサービスを更新されたものに置き換えます。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
