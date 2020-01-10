---
title: 同時実行トークン-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 8a5f3aa09c2a83d5be0998a11ef2ee8100437514
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781145"
---
# <a name="concurrency-tokens"></a>コンカレンシー トークン

> [!NOTE]
> このページでは、同時実行トークンの構成方法について説明します。 EF Core での同時実行制御のしくみと、アプリケーションで同時実行の競合を処理する方法の例については、「[同時実行の競合の処理](../saving/concurrency.md)」を参照してください。

同時実行トークンとして構成されたプロパティは、オプティミスティック同時実行制御を実装するために使用されます。

## <a name="configuration"></a>の構成

### <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Timestamp/rowversion は、行が挿入または更新されるたびに、データベースによって新しい値が自動的に生成されるプロパティです。 また、プロパティは同時実行トークンとして扱われるので、更新しようとしている行がクエリ後に変更された場合に例外が発生することが保証されます。 正確な詳細は、使用されているデータベースプロバイダーによって異なります。SQL Server では、通常、 *byte []* プロパティを使用します。これは、データベースの*ROWVERSION*列として設定されます。

次のように、プロパティを timestamp/rowversion として構成できます。

### <a name="data-annotationstabdata-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-apitabfluent-api"></a>[Fluent API](#tab/fluent-api)

[! code-csharp [Main] (../../../samples/core/Modeling/FluentAPI/Timestamp.cs? name = Timestamp & 強調表示 = 9, 17]

***
