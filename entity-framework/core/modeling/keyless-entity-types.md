---
title: キーなしエンティティ型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 3dbc2700fc9bb277eb90885dfc2506c250ae21f1
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445932"
---
# <a name="keyless-entity-types"></a>キーなしエンティティ型

> [!NOTE]
> この機能は、EF Core 2.1 で、クエリの種類の名前の下に追加されました。 EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。

EF Core モデルには、通常のエンティティ型に加えて、_キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。

## <a name="keyless-entity-types-characteristics"></a>キーなしエンティティ型の特性

キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。 リレーショナルストアでは、fluent API メソッドまたはデータ注釈を使用して、対象のデータベースオブジェクトと列を構成できます。

ただし、これらは通常のエンティティ型とは異なります。

- キーを定義することはできません。
- は_Dbcontext_の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。
- は規約によって検出されません。
- では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、
  - これらは、リレーションシップのプリンシパル end としては機能しません。
  - 所有しているエンティティに対するナビゲーションが含まれていない可能性があります
  - これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。
  - エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。
- @No__t 0 のメソッド呼び出しで構成する必要があります。
- を_定義するクエリ_にマップできます。 定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。

## <a name="usage-scenarios"></a>使用シナリオ

キーなしエンティティ型の主な使用シナリオの一部を次に示します。

- [生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。
- 主キーが含まれていないデータベースビューへのマッピング。
- 主キーが定義されていないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

## <a name="mapping-to-database-objects"></a>データベースオブジェクトへのマッピング

キーなしエンティティ型をデータベースオブジェクトにマップするには、`ToTable` または `ToView` fluent API を使用します。 EF Core の観点から見ると、このメソッドで指定されるデータベースオブジェクトは_ビュー_であり、読み取り専用のクエリソースとして扱われ、update、insert、delete の各操作の対象にすることはできません。 ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。 または、読み取り専用として扱われるデータベーステーブルにすることもできます。 逆に、通常のエンティティ型の場合、EF Core では、`ToTable` メソッドで指定されたデータベースオブジェクトを_テーブル_として扱うことができます。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。 実際には `ToTable` でデータベースビューの名前を指定できます。ビューがデータベースで更新可能になるように構成されていれば、すべてが正常に動作します。

> [!NOTE]
> `ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。

## <a name="example"></a>例

次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。

まず、単純なブログと Post モデルを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

次に、各ブログに関連付けられている投稿の数を照会できる単純なデータベースビューを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

次に、データベースビューの結果を保持するクラスを定義します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

次に、`HasNoKey` API を使用して、 _Onmodelcreating_エンティティ型を構成します。
Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

次に、`DbSet<T>` を含めるように `DbContext` を構成します。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最後に、標準的な方法でデータベースビューに対してクエリを実行できます。

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。
