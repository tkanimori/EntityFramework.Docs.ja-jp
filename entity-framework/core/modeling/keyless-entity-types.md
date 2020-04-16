---
title: キーレス エンティティ型 - EF コア
description: エンティティ フレームワーク コアを使用してキーなしのエンティティ型を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434215"
---
# <a name="keyless-entity-types"></a>キーなしエンティティ型

> [!NOTE]
> この機能は、クエリの種類の名前で EF Core 2.1 で追加されました。 EF Core 3.0 では、この概念はキーレス エンティティ型に変更されました。

通常のエンティティ型に加えて、EF Core モデルには_キーなしのエンティティ型_を含めることができます。

## <a name="defining-keyless-entity-types"></a>キーレス エンティティ型の定義

キーレス エンティティ型は、データ注釈または Fluent API を使用して定義できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>キーなしエンティティタイプの特性

キーレス エンティティ型は、継承マッピングやナビゲーション プロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートします。 リレーショナル ストアでは、fluent API メソッドまたはデータ注釈を使用して、ターゲット データベースのオブジェクトと列を構成できます。

ただし、通常のエンティティ型とは異なります。

- キーを定義することはできません。
- _DbContext_での変更を追跡しないため、データベースに対して挿入、更新、または削除されることはありません。
- 慣例によって発見されることはありません。
- 具体的には、ナビゲーション マッピング機能のサブセットのみをサポートします。
  - 彼らは決して関係の主要な終わりとして機能しないかもしれません。
  - 所有エンティティへのナビゲーションがない場合があります。
  - 通常のエンティティを指す参照ナビゲーション プロパティのみを含めることができます。
  - エンティティには、キーなしのエンティティ型へのナビゲーション プロパティを含めることはできません。
- データ注釈またはメソッド呼び`[Keyless]`出しを使用`.HasNoKey()`して構成する必要があります。
- _定義_クエリ にマップされる可能性があります。 定義クエリは、キーなしエンティティ型のデータ ソースとして機能するモデルで宣言されたクエリです。

## <a name="usage-scenarios"></a>使用シナリオ

キーレス エンティティタイプの主な使用シナリオには、次のものがあります。

- 生の[SQL クエリ](xref:core/querying/raw-sql)の戻り値の型として使用する:
- 主キーを含まないデータベース ビューへのマッピング。
- 主キーが定義されていないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

## <a name="mapping-to-database-objects"></a>データベース オブジェクトへのマッピング

キーなしのエンティティ型をデータベース オブジェクトにマッピングするには、`ToTable`または`ToView`fluent API を使用します。 EF Core の観点からは、このメソッドで指定されたデータベース オブジェクトは_ビュー_であり、読み取り専用のクエリ ソースとして扱われ、更新、挿入、または削除操作の対象になることはできません。 ただし、データベース オブジェクトが実際にデータベース ビューである必要があるという意味ではありません。 また、読み取り専用として扱われるデータベーステーブルを使用することもできます。 逆に、通常のエンティティ型の場合、EF Core は`ToTable`、メソッドで指定されたデータベース オブジェクトを_テーブル_として扱うことができると見なします。 実際には、データベース ビューの名前を指定すると`ToTable`、データベースで更新可能なビューが構成されている限り、すべてが正常に動作するはずです。

> [!NOTE]
> `ToView`オブジェクトはデータベースに既に存在し、移行によって作成されないことを前提としています。

## <a name="example"></a>例

次の例は、キーなしのエンティティ型を使用してデータベース ビューを照会する方法を示しています。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。

まず、単純なブログと投稿モデルを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

次に、各ブログに関連付けられた投稿数を照会できる単純なデータベースビューを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

次に、データベース ビューからの結果を保持するクラスを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

次に、API を使用して_OnModelCreating_でキー`HasNoKey`レス エンティティ型を構成します。
Fluent 構成 API を使用して、キーレス エンティティ型のマッピングを構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

次に、 を`DbContext`含むようにを`DbSet<T>`構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後に、標準的な方法でデータベース ビューを照会できます。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> この型に対するクエリのルートとして機能するコンテキスト レベルのクエリ プロパティ (DbSet) も定義しています。
