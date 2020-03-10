---
title: データのクエリ - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 0e1e50d1a3f647d65301552d0a447f9fcae81438
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413117"
---
# <a name="querying-data"></a>データのクエリ

Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。 LINQ では C# (あるいは自分で選んだ .NET 言語) を使用して、厳密に型指定されたクエリを記述できます。 派生コンテキストとエンティティ クラスを使用して、データベース オブジェクトが参照されます。 EF Core は、LINQ クエリの表現をデータベース プロバイダーに渡します。 その後、データベース プロバイダーがこれをデータベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換します。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

次のスニペットは、Entity Framework Core で一般的なタスクを実現する方法の例を示しています。

## <a name="loading-all-data"></a>すべてのデータの読み込み

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>単一のエンティティの読み込み

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>フィルター処理

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>参考資料

- [LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください
- EF Core でクエリが処理されるしくみについては、「[クエリのしくみ](xref:core/querying/how-query-works)」を参照してください。
