---
title: "同時実行トークン - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: 6574a9098d38c4aa525ffb4896adb01082420b5f
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="concurrency-tokens"></a>同時実行制御トークン

プロパティが、同時実行トークンとして構成されている場合 EF は、その他のユーザーが変更なし、データベース内でその値そのレコードに変更を保存するときにでチェックされます。 EF オプティミスティック同時実行パターンを使用して、つまりは値が変更されていないと想定して、データの保存が、値が変更されたが見つかった場合にスローしようとしています。 します。

構成することがありますたとえば`LastName`で`Person`同時実行トークンであります。 つまり、1 人のユーザーは、いくつかの変更を保存しようとした場合、 `Person`、別のユーザーが変更されて、`LastName`し、例外がスローされます。 アプリケーションがこのレコードが、変更を保存する前にも同じの実際のユーザーを表すことを確認するユーザーの入力を求めるように必要があります。

> [!NOTE]
> このページは、同時実行トークンを構成する方法を説明します。 参照してください[同時実行の処理](../saving/concurrency.md)アプリケーションでオプティミスティック同時実行制御を使用する方法の例についてはします。

## <a name="how-concurrency-tokens-work-in-ef"></a>EF での同時実行トークンのしくみ

データ ストアは、同時実行トークンを更新またはまだ削除されているすべてのレコードが最初、コンテキストはデータベースからデータを読み込むときに割り当てられた同時実行トークンに同じ値を持つことを確認して適用できます。

たとえば、リレーショナル データベースこれを実現するで同時実行トークンを含めることによって、`WHERE`いずれかの句`UPDATE`または`DELETE`コマンドとその影響を受けた行の番号を確認します。 同時実行トークンがまだと一致する場合は、1 つの行が更新されます。 データベース内の値が変更された場合、行は更新されません。

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="conventions"></a>規則

規則では、プロパティは同時実行トークンとして構成ことはありません。

## <a name="data-annotations"></a>データの注釈

同時実行トークンとしてプロパティを構成するのには、データ注釈を使用できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、同時実行トークンとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>タイムスタンプまたは行のバージョン

タイムスタンプは、新しい値がデータベースによって生成された行が挿入または更新するたびに場所プロパティです。 プロパティは、同時実行トークンとしても扱われます。 これにより、その他のユーザー データのクエリを実行するために更新しようとしている行を変更した場合、例外が表示されます。

これを実現する方法については、使用中のデータベース プロバイダーによって決まります。 SQL Server のタイムスタンプは、通常で使用される、 *byte[]*となるプロパティの設定として、 *ROWVERSION*データベース内の列です。

### <a name="conventions"></a>規則

規則では、プロパティはタイムスタンプとして構成されません。

### <a name="data-annotations"></a>データの注釈

データ注釈を使用して、タイムスタンプとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

Fluent API を使用して、タイムスタンプとしてプロパティを構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
