---
title: グローバル クエリ フィルター - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f4ee9b77411290249e763f9cb8492eea61803e91
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124393"
---
# <a name="global-query-filters"></a>グローバル クエリ フィルター

> [!NOTE]
> この機能は EF Core 2.0 で導入されました。

グローバル クエリ フィルターは、(通常 *OnModelCreating* にある) メタデータ モデルのエンティティ型に適用される、LINQ クエリ述語 (通常 LINQ *Where* クエリ演算子に渡されるブール式) です。 このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。 この機能の一般的な用途は次のようになっています。

* **論理削除** - エンティティ型が *IsDeleted* プロパティを定義します。
* **マルチテナント** - エンティティ型が *TenantId* プロパティを定義します。

## <a name="example"></a>例

次の例では、グローバル クエリ フィルターを使用して論理削除とマルチテナントのクエリ動作を単純なブログ モデルに実装する方法を示しています。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)は GitHub で確認できます。

最初に、エンティティを次のように定義します。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

_Blog_ エンティティの _tenantId_ フィールドの宣言に注意してください。 これは、各 Blog インスタンスを特定のテナントに関連付けるのに使用されます。 また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。 これは、_Post_ インスタンスが "論理削除" されたかどうかを追跡するために使用されています。 つまり、インスタンスは、物理的に削除せずに、削除済みとしてマークされます。

次に、`HasQueryFilter` API を使用して _OnModelCreating_ でクエリフィルターを構成します。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

_HasQueryFilter_ の呼び出しに渡される predicate 式は、これらの型の LINQ クエリに自動的に適用されます。

> [!TIP]
> DbContext インスタンスのレベルのフィールドの使用に注意してください。`_tenantId` は現在のテナントを設定するために使用されています。 モデルレベル フィルターは、適切なコンテキストのインスタンス (つまりクエリを実行しているインスタンス) の値を使用します。

> [!NOTE]
> 現在、同一のエンティティに対して複数のクエリフィルターを定義することはできません。最後のフィルターのみが適用されます。 ただし、論理 _AND_ 演算子 ([C# の `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) を使用して、複数の条件を持つ単一のフィルターを定義することができます。

## <a name="disabling-filters"></a>フィルターを無効にする

フィルターは、`IgnoreQueryFilters()` 演算子を使用することで、個々の LINQ クエリに対して無効にできます。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>制限事項

グローバル クエリ フィルターには、次の制限があります。

* フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。
