---
title: グローバル クエリ フィルター - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 4afc9fb0338d34845639d57013ac710445321940
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562443"
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

_Blog_ エンティティの __tenantId_ フィールドの宣言をメモします。 これは、固有のテナントと各 Blog インスタンスの関連付けに使用されます。 また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。 これは、_Post_ インスタンスが "論理削除" されたかどうかの追跡を継続するために使用されます。 つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。

次に、```HasQueryFilter``` API を使用して _OnModelCreating_ でクエリ フィルターを構成します。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

_HasQueryFilter_ 呼び出しに渡される述語式は、型に対応するいずれかの LINQ クエリに自動的に適用されます。

> [!TIP]
> DbContext インスタンス レベルのフィールドの使用に注意してください。```_tenantId``` は、現在のテナントを設定するために使用されます。 モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているインスタンス) の値を使用します。

## <a name="disabling-filters"></a>フィルターを無効にする

フィルターは、```IgnoreQueryFilters()``` 演算子を使用して、個々の LINQ クエリに対して無効にできます。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>制限事項

グローバル クエリ フィルターには、次の制限があります。

* フィルターは、ナビゲーション プロパティへの参照を含むことはできません。
* フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。
