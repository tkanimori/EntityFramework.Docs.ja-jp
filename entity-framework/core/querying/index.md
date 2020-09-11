---
title: データのクエリ - EF Core
description: Entity Framework Core でのクエリに関する情報の概要
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 028c640a17c4946158c86bdf1a663a4050f55921
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616327"
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
