---
title: キーなしエンティティ型-EF Core
description: Entity Framework Core を使用してキーなしエンティティ型を構成する方法
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: cb4ce44526ada77e37eb4dceb9986a670ea3656b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063804"
---
# <a name="keyless-entity-types"></a>キーなしエンティティ型

> [!NOTE]
> この機能は、クエリの種類の名前の下に追加されました。 EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。 `[Keyless]`データ注釈は、EFCore 5.0 で使用できるようになりました。

EF Core モデルには、通常のエンティティ型に加えて、 _キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。

## <a name="defining-keyless-entity-types"></a>キーなしエンティティ型の定義

キーなしエンティティ型は、データ注釈または Fluent API を使用して定義できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>キーなしエンティティ型の特性

キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。 リレーショナルストアでは、fluent API メソッドまたはデータ注釈を使用して、対象のデータベースオブジェクトと列を構成できます。

ただし、これらは通常のエンティティ型とは異なります。

- キーを定義することはできません。
- は _Dbcontext_ の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。
- は規約によって検出されません。
- では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、
  - これらは、リレーションシップのプリンシパル end としては機能しません。
  - 所有しているエンティティに対するナビゲーションが含まれていない可能性があります
  - これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。
  - エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。
- `[Keyless]`データ注釈またはメソッド呼び出しを使用して構成する必要があり `.HasNoKey()` ます。
- を _定義するクエリ_にマップできます。 定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。

## <a name="usage-scenarios"></a>使用シナリオ

キーなしエンティティ型の主な使用シナリオの一部を次に示します。

- [生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。
- 主キーが含まれていないデータベースビューへのマッピング。
- 主キーが定義されていないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

## <a name="mapping-to-database-objects"></a>データベースオブジェクトへのマッピング

キーなしエンティティ型のデータベースオブジェクトへのマッピングは、または fluent API を使用して実現され `ToTable` `ToView` ます。 EF Core の観点から見ると、このメソッドで指定されるデータベースオブジェクトは _ビュー_であり、読み取り専用のクエリソースとして扱われ、update、insert、delete の各操作の対象にすることはできません。 ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。 または、読み取り専用として扱われるデータベーステーブルにすることもできます。 逆に、通常のエンティティ型の場合、EF Core は、メソッドで指定されたデータベースオブジェクトをテーブルとして扱うことができることを前提としてい `ToTable` ます。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。 _table_ 実際には、でデータベースビューの名前を指定でき `ToTable` ます。また、ビューがデータベースで更新可能になるように構成されていれば、すべてが正常に動作します。

> [!NOTE]
> `ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。

## <a name="example"></a>例

次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。

まず、単純なブログと Post モデルを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

次に、各ブログに関連付けられている投稿の数を照会できる単純なデータベースビューを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

次に、データベースビューの結果を保持するクラスを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

次に、API を使用して、 _Onmodelcreating_ エンティティ型を構成し `HasNoKey` ます。
Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

次に、を `DbContext` 含めるようにを構成し `DbSet<T>` ます。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後に、標準的な方法でデータベースビューに対してクエリを実行できます。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。
