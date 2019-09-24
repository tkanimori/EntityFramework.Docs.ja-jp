---
title: 同時実行トークン-EF Core
author: rowanmiller
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: db768c1de99000be91d33764ccd3c3924237f8bb
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197453"
---
# <a name="concurrency-tokens"></a>コンカレンシー トークン

> [!NOTE]
> このページでは、同時実行トークンの構成方法について説明します。 EF Core での同時実行制御のしくみと、アプリケーションで同時実行の競合を処理する方法の例については、「[同時実行の競合の処理](../saving/concurrency.md)」を参照してください。

同時実行トークンとして構成されたプロパティは、オプティミスティック同時実行制御を実装するために使用されます。

## <a name="conventions"></a>規約

慣例により、プロパティは同時実行トークンとして構成されません。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティを同時実行トークンとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティを同時実行トークンとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>タイムスタンプ/行バージョン

Timestamp は、行が挿入または更新されるたびにデータベースによって新しい値が生成されるプロパティです。 プロパティは同時実行トークンとしても扱われます。 これにより、データを照会した後に更新しようとしている行が他のユーザーによって変更された場合に、例外が発生します。

これは、使用されているデータベースプロバイダーによって実現されます。 SQL Server の場合、timestamp は通常、 *byte []* プロパティで使用されます。このプロパティは、データベースの*ROWVERSION*列として設定されます。

### <a name="conventions"></a>規約

慣例により、プロパティはタイムスタンプとして構成されません。

### <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティをタイムスタンプとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティをタイムスタンプとして構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs#ConfigureTimestampFluent)]
