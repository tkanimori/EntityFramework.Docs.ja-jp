---
title: 同時実行トークン - EF Core
author: rowanmiller
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 0051d416544a11385f99d36e45843c5b20725af7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994227"
---
# <a name="concurrency-tokens"></a>同時実行制御トークン

> [!NOTE]
> このページは、同時実行制御トークンを構成する方法を説明します。 参照してください[同時実行競合の処理](../saving/concurrency.md)EF Core と、アプリケーションで同時実行の競合を処理する方法の例での同時実行制御のしくみの詳細についてはします。

同時実行トークンとして構成されているプロパティは、オプティミスティック同時実行制御を実装するために使用されます。

## <a name="conventions"></a>規約

慣例により、プロパティは同時実行トークンとして構成されることはありません。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、同時実行トークンとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、同時実行トークンとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>タイムスタンプや行のバージョン

タイムスタンプは、行が挿入または更新されるたびにデータベースによってで新しい値が生成された場所のプロパティです。 プロパティは、同時実行トークンとしても扱われます。 これにより、他のユーザー データのクエリを実行するために更新しようとしている行を変更した場合、例外が発生します。

これを実現する方法は、使用中のデータベース プロバイダーの責任です。 For SQL Server をタイムスタンプは通常の使用、 *byte[]* としては、プロパティの設定、 *ROWVERSION*データベース内の列。

### <a name="conventions"></a>規約

慣例により、プロパティはタイムスタンプとして構成されることはありません。

### <a name="data-annotations"></a>データの注釈

データ注釈を使用して、タイムスタンプとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

タイムスタンプ プロパティを構成するのには、Fluent API を使用できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
