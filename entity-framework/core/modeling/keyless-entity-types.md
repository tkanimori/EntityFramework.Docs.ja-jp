---
title: キーなしエンティティ型-EF Core
description: Entity Framework Core を使用してキーなしエンティティ型を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 520c9ed93240c05deee36fa527a3757490fd7082
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414645"
---
# <a name="keyless-entity-types"></a>キーなしエンティティ型

> [!NOTE]
> この機能は、EF Core 2.1 で、クエリの種類の名前の下に追加されました。 EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。

EF Core モデルには、通常のエンティティ型に加えて、_キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。

## <a name="keyless-entity-types-characteristics"></a>キーなしエンティティ型の特性

キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。 リレーショナル ストアで、ターゲット データベースのオブジェクトと列 fluent API のメソッドまたはデータの注釈を使用して構成できます。

ただし、これらは通常のエンティティ型とは異なります。

- キーを定義することはできません。
- は_Dbcontext_の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。
- 規則によって検出されたことはありません。
- では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、
  - リレーションシップのプリンシパル end として機能しない可能性があります。
  - 所有しているエンティティに対するナビゲーションが含まれていない可能性があります
  - これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。
  - エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。
- `.HasNoKey()` メソッド呼び出しを使用して構成する必要があります。
- を_定義するクエリ_にマップできます。 定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。

## <a name="usage-scenarios"></a>使用シナリオ

キーなしエンティティ型の主な使用シナリオの一部を次に示します。

- [生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。
- 主キーが含まれていないデータベースビューへのマッピング。
- 定義された主キーがないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

## <a name="mapping-to-database-objects"></a>データベース オブジェクトへのマッピング

キーなしエンティティ型をデータベースオブジェクトにマップするには、`ToTable` または `ToView` fluent API を使用します。 EF Core の観点から見ると、このメソッドで指定されるデータベースオブジェクトは_ビュー_であり、読み取り専用のクエリソースとして扱われ、update、insert、delete の各操作の対象にすることはできません。 ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。 または、読み取り専用として扱われるデータベーステーブルにすることもできます。 逆に、通常のエンティティ型の場合、EF Core は、`ToTable` メソッドに指定されたデータベースオブジェクトを_テーブル_として扱うことができることを前提としています。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。 実際には、`ToTable` でデータベースビューの名前を指定できます。ビューがデータベースで更新可能になるように構成されていれば、すべてが正常に動作します。

> [!NOTE]
> `ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。

## <a name="example"></a>例

次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。

最初に、ブログや投稿の単純なモデルを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

次に、各ブログに関連する投稿の数を照会することを許可する単純なデータベース ビューを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

次に、データベース ビューから結果を保持するクラスを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

次に、`HasNoKey` API を使用して、 _Onmodelcreating_エンティティ型を構成します。
Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

次に、`DbSet<T>`を含めるように `DbContext` を構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後に、標準的な方法で、データベース ビューを照会できます。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。
