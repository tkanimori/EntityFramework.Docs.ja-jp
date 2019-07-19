---
title: クエリの種類 - EF Core
author: anpete
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/query-types
ms.openlocfilehash: 6f0f860c6a4e619e13d55e6207234a8b5261ee09
ms.sourcegitcommit: d1230e34673b8323a227ab37958dfa77f3684728
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68330792"
---
# <a name="query-types"></a>クエリの種類
> [!NOTE]
> この機能は EF Core 2.1 の新機能

エンティティ型だけでなく、EF Core モデルを含めることができます_型をクエリ_エンティティ型にマップされていないデータに対してデータベース クエリを実行するために使用できます。

## <a name="compare-query-types-to-entity-types"></a>エンティティ型にクエリの種類を比較します。

クエリの種類はそのエンティティ型と同様にします。

- いずれかのモデルに追加できますで`OnModelCreating`または派生の"set"プロパティを使用して_DbContext_します。
- 継承のマッピングとナビゲーション プロパティと同様に、同じマッピング機能の多くをサポートします。 リレーショナル ストアで、ターゲット データベースのオブジェクトと列 fluent API のメソッドまたはデータの注釈を使用して構成できます。

ただし、それらがエンティティから異なる型にします。

- 定義するキーは必要ありません。
- 変更追跡は決して、 _DbContext_したがっては決して挿入、更新またはデータベースの削除します。
- 規則によって検出されたことはありません。
- 具体的には、ナビゲーションのマッピング機能のサブセットをサポートのみ。
  - リレーションシップのプリンシパル end として機能しない可能性があります。
  - エンティティを指す参照ナビゲーション プロパティのみを含めることができます。
  - エンティティは、クエリの型にナビゲーション プロパティを含めることはできません。
- 対処する、 _ModelBuilder_を使用して、`Query`メソッドではなく、`Entity`メソッド。
- マップされて、 _DbContext_型のプロパティを介して`DbQuery<T>`なく `DbSet<T>`
- 使用してデータベース オブジェクトにマップされて、`ToView`メソッド、なく`ToTable`します。
- マップすることがあります、_クエリを定義する_- クエリの種類のデータ ソースが機能するモデルで宣言されている 2 番目のクエリは、クエリを定義します。

## <a name="usage-scenarios"></a>使用シナリオ

クエリの種類の主な使用シナリオを次に示します。

- アドホックの戻り値の型として使用される`FromSql()`クエリ。
- データベース ビューへのマッピング。
- 定義された主キーがないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

## <a name="mapping-to-database-objects"></a>データベース オブジェクトへのマッピング

使用して実現は、データベース オブジェクトにクエリ型のマッピング、 `ToView` fluent API。 このメソッドで指定されたデータベース オブジェクトは、EF Core の観点から、_ビュー_、読み取り専用クエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作。 ただし、これは限りませんするデータベース オブジェクトがあるデータベース ビューを実際に必要 - 読み取り専用として扱われるデータベース テーブルにもできます。 逆に、エンティティ型の場合は、EF Core と想定でデータベース オブジェクトが指定されている、`ToTable`としてメソッドを処理できる、_テーブル_、つまりクエリのソースとして使用できますが、削除、更新プログラムも対象となる、および挿入操作です。 実際には、データベース ビューの名前を指定できます`ToTable`データベースに対して更新可能にするビューが構成されている限り問題なく機能すべて。

## <a name="example"></a>例

次の例は、クエリの種類を使用して、データベース ビューをクエリする方法を示しています。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes)は GitHub で確認できます。

最初に、ブログや投稿の単純なモデルを定義します。

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

次に、各ブログに関連する投稿の数を照会することを許可する単純なデータベース ビューを定義します。

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

次に、データベース ビューから結果を保持するクラスを定義します。

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

次に、クエリの種類で構成_OnModelCreating_を使用して、 `modelBuilder.Query<T>` API。
クエリの種類のマッピングを構成するのに標準の fluent 構成 Api を使用します。

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

次に、を`DbContext` `DbQuery<T>`含めるようにを構成します。[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#DbQuery)]

最後に、標準的な方法で、データベース ビューを照会できます。

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティも定義しておいたに注意してください。
