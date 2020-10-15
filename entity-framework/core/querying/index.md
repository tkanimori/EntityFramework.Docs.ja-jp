---
title: データのクエリ - EF Core
description: Entity Framework Core でのクエリに関する情報の概要
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 7cf53d59a577c5bc45f3ea4ea0cfbe257437b44e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062564"
---
# <a name="querying-data"></a>データのクエリ

Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。 LINQ では C# (あるいは自分で選んだ .NET 言語) を使用して、厳密に型指定されたクエリを記述できます。 派生コンテキストとエンティティ クラスを使用して、データベース オブジェクトが参照されます。 EF Core は、LINQ クエリの表現をデータベース プロバイダーに渡します。 その後、データベース プロバイダーがこれをデータベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換します。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview)は GitHub で確認できます。

次のスニペットは、Entity Framework Core で一般的なタスクを実現する方法の例を示しています。

## <a name="loading-all-data"></a>すべてのデータの読み込み

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>単一のエンティティの読み込み

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a>フィルター処理

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a>参考資料

- [LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください
- EF Core でクエリが処理されるしくみについては、「[クエリのしくみ](xref:core/querying/how-query-works)」を参照してください。
