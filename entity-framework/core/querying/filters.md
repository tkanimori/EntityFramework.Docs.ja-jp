---
title: グローバル クエリ フィルター - EF Core
description: Entity Framework Core で結果をフィルター処理することを目的としたグローバル クエリ フィルターの使用
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 6436f9f8e2e09d44ef9528fd2022720d40095fe0
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430132"
---
# <a name="global-query-filters"></a>グローバル クエリ フィルター

グローバル クエリ フィルターは、(通常 `OnModelCreating` にある) メタデータ モデルのエンティティ型に適用される LINQ クエリ述語です。 クエリ述語は、通常、LINQ の `Where` クエリ演算子に渡されるブール式です。  このようなフィルターは、EF Core によって、これらのエンティティ型に関係する LINQ クエリに自動的に適用されます。  また、EF Core によって、Include またはナビゲーション プロパティを使用して間接的に参照されるエンティティ型にも適用されます。 この機能の一般的な用途は次のようになっています。

* **論理削除** - エンティティ型が `IsDeleted` プロパティを定義します。
* **マルチテナント** - エンティティ型が `TenantId` プロパティを定義します。

## <a name="example"></a>例

次の例では、グローバル クエリ フィルターを使用して、マルチテナントおよび論理的な削除のクエリ動作を単純なブログ モデルに実装する方法を示しています。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters)は GitHub で確認できます。

最初に、エンティティを次のように定義します。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

`Blog` エンティティの `_tenantId` フィールドの宣言をメモします。 このフィールドは、各 Blog インスタンスを固有のテナントに関連付けるために使用されます。 また、`Post` エンティティ型の `IsDeleted` プロパティが定義されています。 このプロパティは、Post インスタンスが "論理的に削除" されたかどうかを追跡するために使用されます。 つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。

次に、`HasQueryFilter` API を使用して `OnModelCreating` でクエリフィルターを構成します。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

`HasQueryFilter` の呼び出しに渡される predicate 式は、これらの型の LINQ クエリに自動的に適用されます。

> [!TIP]
> DbContext インスタンス レベルのフィールドの使用に注意してください。`_tenantId` は、現在のテナントを設定するために使用されます。 モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているインスタンス) の値を使用します。

> [!NOTE]
> 現在、同じエンティティに対して複数のクエリ フィルターを定義することはできません。最後のフィルターのみが適用されます。 ただし、論理 `AND` 演算子 ([C# では `&&`](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) を使用して、複数の条件を持つ単一のフィルターを定義することができます。

## <a name="use-of-navigations"></a>ナビゲーションの使用

グローバル クエリ フィルターを定義するときにも、ナビゲーションを使用することができます。 クエリ フィルターでナビゲーションを使用すると、クエリ フィルターが再帰的に適用されます。 クエリ フィルターで使用されているナビゲーションが EF Core によって拡張されると、参照先エンティティで定義されているクエリ フィルターも適用されます。

これを説明するために、次のように `OnModelCreating` でクエリ フィルターを構成します。[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]

次に、すべての `Blog` エンティティに対してクエリを実行します。[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]

このクエリでは、次の SQL が生成されます。これにより、`Blog` と `Post` の両方のエンティティに定義されたクエリ フィルターが適用されます。

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> 現在、グローバル クエリ フィルター定義内のサイクルは EF Core によって検出されないため、それらを定義する際には注意が必要です。 正しく指定されていない場合は、クエリの変換中にサイクルが無限ループになる可能性があります。

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>必須のナビゲーションを使用したクエリ フィルターを含むエンティティへのアクセス

> [!CAUTION]
> グローバル クエリ フィルターが定義されているエンティティにアクセスする場合に必須のナビゲーションを使用すると、予期しない結果が生じる可能性があります。

必須のナビゲーションでは、関連エンティティが常に存在している必要があります。 必要な関連エンティティがクエリ フィルターによって除外されると、親エンティティも結果に存在しなくなります。 そのため、結果で取得できる要素が予想よりも少ない可能性があります。

この問題を説明するために、上で指定した `Blog` および `Post` エンティティと、次の `OnModelCreating` メソッドを使用できます。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

このモデルは、次のデータを使用してシード処理することができます。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

次の 2 つのクエリを実行すると、問題が発生する可能性があります。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

上記の設定の場合、最初のクエリでは 6 つの `Post` がすべて返されますが、2 つめのクエリで返されるのは 3 つのみです。 このような不一致は、2 つめのクエリ内の `Include` メソッドによって、関連する `Blog` エンティティが読み込まれるために発生します。 `Blog` と `Post` 間のナビゲーションは必須であるため、EF Core ではクエリの作成時に `INNER JOIN` が使用されます。

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

`INNER JOIN` を使用すると、関連する `Blog` がグローバル クエリ フィルターによって削除されたすべての `Post` が除外されます。

そのアドレス指定は、必須ではなく、オプションのナビゲーションを使用して行うことができます。
このように、最初のクエリは以前と同じままですが、2 番目のクエリでは `LEFT JOIN` が生成され、6 個の結果が返されるようになります。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

別の方法として、`Blog` と `Post` の両方のエンティティに対して一貫したフィルターを指定する方法があります。
これにより、一致するフィルターが `Blog` と `Post` の両方に適用されます。 予期しない状態になる可能性がある `Post` は削除され、両方のクエリとも 3 個の結果が返されます。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a>フィルターを無効にする

フィルターは、<xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> 演算子を使用することで、個々の LINQ クエリに対して無効にできます。

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>制限事項

グローバル クエリ フィルターには、次の制限があります。

* フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。
