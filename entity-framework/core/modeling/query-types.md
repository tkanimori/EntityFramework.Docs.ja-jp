---
title: クエリの種類]-[EF コア
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: f16e3a130f3a4f92b2bf6014f2df0ca4eec56a25
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="query-types"></a>クエリの種類
> [!NOTE]
> この機能は、EF コア 2.1 の新機能

エンティティ型だけでなく、EF の主要なモデルを含めることができます_クエリ タイプ_エンティティ型にマップされていないデータに対するデータベース クエリを実行するために使用できます。

クエリの種類では、エンティティの種類と多くの類似点があります。

- 追加することも、モデルにいずれかで`OnModelCreating`、または、派生の「設定」プロパティを介して_DbContext_です。
- 多くのマッピング、(以下の制限事項を参照してください) のナビゲーション プロパティの継承と同様に、リレーショナル ストア、ターゲット データベースのオブジェクトと fluent API のメソッドまたはデータ注釈を使用して列を構成する機能は同じのマッピング機能をサポートしています。

ただしはエンティティを異なる型です。

- 定義するキーは不要です。
- 変更追跡されることはありません、 _DbContext_とそのためことはありません挿入、更新または削除、データベースでします。
- 規則によって検出されることはありません。
- 具体的には、ナビゲーションのマッピング機能 - のサブセットをサポートのみ。
  - リレーションシップのプリンシパル end として機能する可能性があることはありません。
  - エンティティを指す参照ナビゲーション プロパティのみを含み、ことができます。
  - エンティティは、クエリの型にナビゲーション プロパティを含めることはできません。
- 対処する、 _ModelBuilder_を使用して、`Query`メソッドではなく、`Entity`メソッドです。
- マップされて、 _DbContext_型のプロパティを介して`DbQuery<T>`なく `DbSet<T>`
- 使用してデータベース オブジェクトにマップされて、`ToView`メソッド、なく`ToTable`です。
- マップすることがあります、_クエリを定義する_- クエリの種類のデータ ソースは、機能、モデルで宣言されているセカンダリのクエリは、クエリを定義します。

クエリの種類の主な使用シナリオを次に示します。

- 戻り値の型として機能しているアドホック`FromSql()`クエリ。
- データベース ビューへのマッピング。
- 定義された主キーがないテーブルへのマッピング。
- モデルで定義されているクエリへのマッピング。

> [!TIP]
> 使用して実現は、データベース オブジェクトへのクエリの種類のマッピング、 `ToView` fluent API です。 このメソッドで指定されたデータベース オブジェクトは、EF コアの観点から、_ビュー_、読み取り専用のクエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作します。 ただし、これはいないというデータベース オブジェクトが実際には、データベース ビューを指定するために必要 - 読み取り専用として扱われるデータベース テーブルに代わりにできること。 逆に、エンティティ型の EF コア前提としていますにデータベース オブジェクトが指定されている、`ToTable`としてメソッドを処理できる、_テーブル_クエリのソースとして使用できますけれども、削除、更新プログラムも対象となることを意味、および挿入操作です。 実際には、データベース ビューの名前を指定できます`ToTable`うまく動作するでしょうデータベースで更新できるビューが構成されている限り、します。

## <a name="example"></a>例

次の例では、クエリの種類を使用して、データベース ビューをクエリする方法を示します。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes)は GitHub で確認できます。

最初に、単純なブログと Post のモデルを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

次に、各ブログに関連する投稿の数を照会できる単純なデータベース ビューを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

次に、データベース ビューから結果を保持するクラスを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

クエリの種類を次に、構成_OnModelCreating_を使用して、 `modelBuilder.Query<T>` API です。
標準 fluent 構成 Api を使用して、クエリの種類のマッピングを構成します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

最後に、データベース ビューをクエリして標準の方法で。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティを定義していますも注意してください。
