---
title: "グローバル クエリのフィルター]-[EF コア"
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="global-query-filters"></a>グローバル クエリのフィルター

グローバル クエリのフィルターは、LINQ クエリの述語 (ブール式は通常、LINQ に渡される*場所*クエリ演算子) メタデータ モデルのエンティティ型に適用される (通常の*OnModelCreating*)。 このようなフィルターは、これらのエンティティ型、エンティティ型のインクルードを使用して間接的に、ように参照または直接のナビゲーション プロパティの参照の追加に関連する LINQ クエリを自動的に適用されます。 この機能の一般的なアプリケーションは次のとおりです。

* **論理削除**-エンティティの種類の定義、 *IsDeleted*プロパティです。
* **マルチ テナント**-エンティティの種類の定義、 *TenantId*プロパティです。

## <a name="example"></a>例

次の例では、グローバル クエリ フィルターを使用して単純なブログ モデルでマルチ テナント機能およびソフト削除クエリの動作を実装する方法を示します。

> [!TIP]
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters)GitHub でします。

最初に、エンティティを定義します。

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

_ の宣言に注意してください_tenantId_フィールドに、_ブログ_エンティティです。 各ブログ インスタンスを特定のテナントに関連付けるために使用されます。 定義されても、 _IsDeleted_プロパティを_Post_エンティティ型。 これは、使用するかどうかを把握しておく、 _Post_インスタンスは、「論理削除済み」されました。 例。インスタンスは、基になるデータを物理的に削除する削除済み withouth としてマークされます。

次に、フィルターを構成、クエリで_OnModelCreating_を使用して、 ```HasQueryFilter``` API です。

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

渡される述語の式、 _HasQueryFilter_それらの型の任意の LINQ クエリに自動的に適用されるようになりました呼び出しです。

> [!TIP]
> DbContext インスタンス レベルのフィールドの使用に注意してください:```_tenantId```現在のテナントを設定するために使用します。 モデル レベルのフィルターでは、正しいコンテキスト インスタンスから値を使用します。 例。クエリを実行しているインスタンス。

## <a name="disabling-filters"></a>フィルターを無効にします。

フィルターを無効にする個々 の LINQ クエリを使用して、```IgnoreQueryFilters()```演算子。

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>制限事項

グローバル クエリのフィルターには、次の制限があります。

* フィルターは、ナビゲーション プロパティへの参照を含めることはできません。
* フィルターは、継承階層のエンティティ型のルートに対してのみ定義できます。
