---
title: 単一クエリと分割クエリ - EF Core
description: Entity Framework Core の SQL での単一クエリと分割クエリへのクエリの変換
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 8615e7ba5247a90a1f980e9f7b1b23c81170971f
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128993"
---
# <a name="split-queries"></a>分割クエリ

## <a name="single-queries"></a>単一のクエリ

リレーショナル データベースの場合、単一クエリで JOIN を使用すると、関連するすべてのエンティティが読み込まれます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製されます。 この重複により、いわゆる "デカルト爆発" の問題が発生します。 さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。

## <a name="split-queries"></a>分割クエリ

> [!NOTE]
> この機能は EF Core 5.0 で導入されました。 `Include` を使用した場合にのみ機能します。 [このイシュー](https://github.com/dotnet/efcore/issues/21234)により、`Include` を使用しないプロジェクションで関連データを読み込むときの分割クエリのサポートが追跡されています。

EF では、特定の LINQ クエリを複数の SQL クエリに "*分割*" するように指定できます。 分割クエリを使用すると、JOIN ではなく、含まれているコレクション ナビゲーションごとに追加の SQL クエリが生成されます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

これにより、次の SQL が生成されます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> 一対一で関連するエンティティは、パフォーマンスへの影響がないため、常に同じクエリ内で JOIN によって読み込まれます。

## <a name="enabling-split-queries-globally"></a>分割クエリをグローバルに有効にする

分割クエリは、アプリケーションのコンテキストで既定値として構成することもできます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

分割クエリが既定値として構成されている場合でも、特定のクエリを単一のクエリとして実行するように構成することができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

どのような構成も存在しない場合は、単一クエリ モードが EF Core によって既定で使用されます。 パフォーマンスの問題が発生する可能性があるため、次の条件が満たされるときは常に EF Core によって警告が生成されます。

- クエリで複数のコレクションが読み込まれることが EF Core によって検出された。
- ユーザーがクエリ分割モードをグローバルに構成していない。
- ユーザーがクエリで `AsSingleQuery`/`AsSplitQuery` 演算子を使用していない。

警告をオフにするには、クエリ分割モードをグローバルに、またはクエリ レベルで、適切な値に構成します。

## <a name="characteristics-of-split-queries"></a>分割クエリの特性

分割クエリによって JOIN とデカルト爆発に関連するパフォーマンス上の問題が回避されますが、いくつかの欠点もあります。

- ほとんどのデータベースでは単一クエリに対してデータの整合性が保証されますが、複数クエリに対してこのような保証は存在しません。 クエリの実行と同時にデータベースが更新された場合、生成されるデータの整合性が失われる可能性があります。 これはシリアル化可能なトランザクションまたはスナップショット トランザクションでクエリをラップすることで軽減できますが、これにより、それ自体のパフォーマンス上の問題が発生する可能性があります。 詳細については、ご利用のデータベースのドキュメントを参照してください。
- 現在、各クエリは、データベースに対する追加のネットワーク ラウンドトリップを意味します。 特にデータベースの待機時間が長い場合 (クラウド サービスなど)、複数のネットワーク ラウンドトリップによってパフォーマンスが低下する可能性があります。
- 一部のデータベースでは、複数のクエリの結果を同時に使用することが許可されていますが (SQL Server と MARS、Sqlite)、ほとんどの場合、特定の時点でアクティブにできるクエリは 1 つだけです。 そのため、後のクエリを実行する前に、前のクエリの結果をすべてアプリケーションのメモリにバッファーする必要があります。これにより、メモリ要件が大きくなります。

残念ながら、すべてのシナリオに適合する関連エンティティの読み込み方法はありません。 単一クエリと分割クエリの長所と短所を慎重に検討し、ニーズに合うものを選択してください。
