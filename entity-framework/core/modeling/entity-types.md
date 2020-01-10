---
title: エンティティ型-EF Core
description: Entity Framework Core を使用してエンティティ型を構成およびマップする方法
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502415"
---
# <a name="entity-types"></a>エンティティ型

型の DbSet をコンテキストに含めることは、それが EF Core のモデルに含まれることを意味します。通常、このような型は*エンティティ*として参照されます。 EF Core は、データベースとの間でエンティティインスタンスの読み取りと書き込みを行うことができます。リレーショナルデータベースを使用している場合は、EF Core によって、移行によってエンティティのテーブルを作成できます。

## <a name="including-types-in-the-model"></a>モデルに型を含める

慣例により、コンテキストの DbSet プロパティで公開される型は、エンティティとしてモデルに含まれます。 `OnModelCreating` メソッドに指定されているエンティティ型は、他の検出されたエンティティ型のナビゲーションプロパティを再帰的に調べることによって検出される型と同様にも含まれます。

以下のコードサンプルでは、すべての型が含まれています。

* `Blog` は、コンテキストの DbSet プロパティで公開されているため、含まれています。
* `Post` は、`Blog.Posts` ナビゲーションプロパティを使用して検出されるため、含まれています。
* `OnModelCreating`に指定されているため、`AuditEntry` します。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>モデルから型を除外する

モデルに型を含めない場合は、その型を除外することができます。

### <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a>テーブル名

慣例により、各エンティティ型は、エンティティを公開する DbSet プロパティと同じ名前のデータベーステーブルにマップされるように設定されます。 指定されたエンティティに DbSet が存在しない場合は、クラス名が使用されます。

テーブル名は手動で構成できます。

### <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>テーブル スキーマ

リレーショナルデータベースを使用する場合、テーブルはデータベースの既定のスキーマで作成されます。 たとえば、Microsoft SQL Server は `dbo` スキーマを使用します (SQLite はスキーマをサポートしていません)。

次のように、特定のスキーマで作成されるテーブルを構成できます。

### <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

各テーブルのスキーマを指定するのではなく、fluent API を使用してモデルレベルで既定のスキーマを定義することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

既定のスキーマを設定すると、シーケンスなどの他のデータベースオブジェクトも影響を受けることに注意してください。
