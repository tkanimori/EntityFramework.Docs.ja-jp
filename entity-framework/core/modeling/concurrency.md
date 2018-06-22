---
title: 同時実行トークン - EF コア
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: f3cf28d5c54e63aa76058e9fe1d9f3de5b37d579
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
ms.locfileid: "29745488"
---
# <a name="concurrency-tokens"></a>同時実行制御トークン

> [!NOTE]
> このページは、同時実行トークンを構成する方法を説明します。 参照してください[同時実行の競合の処理](../saving/concurrency.md)の EF コアと、アプリケーションで同時実行の競合を処理する方法の例での同時実行制御のしくみの詳細についてはします。

オプティミスティック同時実行制御を実装するのには、同時実行トークンとして構成されているプロパティを使用します。

## <a name="conventions"></a>規約

規則では、プロパティは同時実行トークンとして構成ことはありません。

## <a name="data-annotations"></a>データの注釈

同時実行トークンとしてプロパティを構成するのには、データ注釈を使用できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、同時実行トークンとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>タイムスタンプまたは行のバージョン

タイムスタンプは、新しい値がデータベースによって生成された行が挿入または更新するたびに場所プロパティです。 プロパティは、同時実行トークンとしても扱われます。 これにより、その他のユーザー データのクエリを実行するために更新しようとしている行を変更した場合、例外が表示されます。

これを実現する方法については、使用中のデータベース プロバイダーによって決まります。 SQL Server のタイムスタンプは、通常で使用される、 *byte[]* となるプロパティの設定として、 *ROWVERSION*データベース内の列です。

### <a name="conventions"></a>規約

規則では、プロパティはタイムスタンプとして構成されません。

### <a name="data-annotations"></a>データの注釈

データ注釈を使用して、タイムスタンプとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

Fluent API を使用して、タイムスタンプとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
