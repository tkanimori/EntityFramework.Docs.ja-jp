---
title: エンティティ型-EF Core
description: Entity Framework Core を使用してエンティティ型を構成およびマップする方法
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: bfefa29c08679a1524c00769b3495d75a301e2d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062231"
---
# <a name="entity-types"></a>エンティティ型

型の DbSet をコンテキストに含めることは、それが EF Core のモデルに含まれることを意味します。通常、このような型は *エンティティ*として参照されます。 EF Core は、データベースとの間でエンティティインスタンスの読み取りと書き込みを行うことができます。リレーショナルデータベースを使用している場合は、EF Core によって、移行によってエンティティのテーブルを作成できます。

## <a name="including-types-in-the-model"></a>モデルに型を含める

慣例により、コンテキストの DbSet プロパティで公開される型は、エンティティとしてモデルに含まれます。 メソッドに指定されているエンティティ型は `OnModelCreating` 、他の検出されたエンティティ型のナビゲーションプロパティを再帰的に調べることによって検出される型と同様にも含まれます。

以下のコードサンプルでは、すべての型が含まれています。

* `Blog` は、コンテキストの DbSet プロパティで公開されているため、含まれています。
* `Post` は、ナビゲーションプロパティを使用して検出されるため、含まれてい `Blog.Posts` ます。
* `AuditEntry` はで指定されているため `OnModelCreating` です。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>モデルから型を除外する

モデルに型を含めない場合は、その型を除外することができます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>移行から除外する

> [!NOTE]
> EF Core 5.0 では、移行からテーブルを除外する機能が追加されました。

同じエンティティ型を複数の型にマップすると便利な場合があり `DbContext` ます。 これは、境界付けられたコンテキストごとに異なる型を使用することが一般的である、境界付けられた [コンテキスト](https://www.martinfowler.com/bliki/BoundedContext.html)を使用する場合に特に当てはまり `DbContext` ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

この構成を使用すると、テーブルは作成されませんが、 `blogs` `Blog` モデルに含まれ、通常どおりに使用できます。

移行を使用してテーブルの管理を再開する必要がある場合は、が除外されないように新しい移行を作成する必要があり `blogs` ます。 次の移行には、テーブルに加えられた変更がすべて含まれるようになります。

## <a name="table-name"></a>テーブル名

慣例により、各エンティティ型は、エンティティを公開する DbSet プロパティと同じ名前のデータベーステーブルにマップされるように設定されます。 指定されたエンティティに DbSet が存在しない場合は、クラス名が使用されます。

テーブル名は手動で構成できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>テーブル スキーマ

リレーショナルデータベースを使用する場合、テーブルはデータベースの既定のスキーマで作成されます。 たとえば、Microsoft SQL Server はスキーマを使用し `dbo` ます (SQLite はスキーマをサポートしていません)。

次のように、特定のスキーマで作成されるテーブルを構成できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

各テーブルのスキーマを指定するのではなく、fluent API を使用してモデルレベルで既定のスキーマを定義することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

既定のスキーマを設定すると、シーケンスなどの他のデータベースオブジェクトも影響を受けることに注意してください。

## <a name="view-mapping"></a>マッピングの表示

エンティティ型は、Fluent API を使用してデータベースビューにマップできます。

> [!Note]
> EF では、参照されているビューが既にデータベースに存在することを前提としています。このビューは、移行時に自動的に作成されません。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 ビューにマップすると、既定のテーブルマッピングが削除されますが、エンティティ型を明示的にテーブルにマップすることもできます。 この場合、クエリにはクエリマッピングが使用され、更新にはテーブルマッピングが使用されます。
