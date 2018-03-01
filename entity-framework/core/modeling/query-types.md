---
title: "クエリの種類]-[EF コア"
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: d03c4b1d5635530e63b93e051cb69583718deb4e
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="query-types"></a>クエリの種類
> [!NOTE]
> この機能は、EF コア 2.1 の新機能

クエリの種類は、EF の主要なモデルに追加できる読み取り専用のクエリの結果型です。 クエリの型 (匿名型の場合) などのアドホック クエリを有効にするがより柔軟なマッピングの構成に指定できるためです。

そのエンティティ型に概念的に似ています。

- POCO (C#) のいずれかと、モデルに追加される型では```OnModelCreating```を使用して、```ModelBuilder.Query```メソッド、または DbContext「セット」プロパティを介して (クエリがこのようなプロパティを型として型指定されて```DbQuery<T>```ではなく```DbSet<T>```)。
- 正規のエンティティ型と同じマッピング機能の多くをサポートしています。 たとえば、継承の割り当て、ナビゲーション (limitiations 以下を参照してください) と、リレーショナル ストアを使用してターゲット データベースのスキーマ オブジェクトを構成する機能の```ToTable```、 ```HasColumn``` fluent api のメソッド (またはデータ注釈)。

クエリの種類はエンティティを異なるの型します。

- 定義するキーは不要です。
- 変更トラッカーによって追跡されてことはありません。
- 規則によって検出されることはありません。
- サブセットのみがサポートのナビゲーションのマッピング機能 - 具体的には、リレーションシップのプリンシパル end として機能しない可能性があります。
- マップすることがあります、_クエリを定義する_-A を定義するクエリはセカンダリのクエリは、データ ソースがクエリの種類の機能です。

クエリの種類の主な使用シナリオを次に示します。

- データベース ビューへのマッピング。
- 定義された主キーがないテーブルへのマッピング。
- 戻り値の型として機能しているアドホック```FromSql()```クエリ。
- モデルで定義されているクエリへのマッピング。

> [!TIP]
> 使用して実現は、データベース ビューをクエリの種類のマッピング、 ```ToTable``` fluent API です。

## <a name="example"></a>例

次の例では、クエリの種類を使用して、データベース ビューをクエリする方法を示します。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes)は GitHub で確認できます。

最初に、単純なブログと Post のモデルを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

次に、各ブログに関連する投稿の数を照会できる単純なデータベース ビューを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

クエリの種類を次に、構成_OnModelCreating_を使用して、 ```modelBuilder.Query<T>``` API です。
標準 fluent 構成 Api を使用して、クエリの種類のマッピングを構成します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

最後に、データベース ビューをクエリして標準の方法で。

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティを定義していますも注意してください。
