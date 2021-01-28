---
title: 生成された値-EF Core
description: Entity Framework Core を使用する場合のプロパティの値生成の構成方法
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983548"
---
# <a name="generated-values"></a>生成された値

データベース列の値は、さまざまな方法で生成できます。主キー列は、自動インクリメントの整数、他の列には既定値または計算値などがあります。このページでは、EF Core で構成値を生成するためのさまざまなパターンについて詳しく説明します。

## <a name="default-values"></a>既定値

リレーショナルデータベースでは、既定値を使用して列を構成できます。その列の値を指定せずに行が挿入されると、既定値が使用されます。

プロパティの既定値を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

既定値の計算に使用される SQL フラグメントを指定することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>計算列

ほとんどのリレーショナルデータベースでは、データベース内の値を計算するように列を構成できます。通常は、他の列を参照する式を使用します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

上の例では、データベースからフェッチされるたびに値が計算される、 *仮想* 計算列が作成されます。 計算列を *格納* するように指定することもできます *。これ* は、計算列が行のすべての更新で計算され、通常の列と共にディスクに格納されることを意味します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> 格納されている計算列の作成のサポートが EF Core 5.0 で追加されました。

## <a name="primary-keys"></a>主キー

慣例として、short、int、long、または Guid 型の非複合主キーは、アプリケーションによって値が提供されない場合に、挿入されたエンティティに対して値が生成されるように設定されます。 通常、データベースプロバイダーは必要な構成を行います。たとえば、SQL Server の数値主キーは、ID 列として自動的に設定されます。

詳細については、 [キーに関するドキュメントを参照してください](xref:core/modeling/keys)。

## <a name="explicitly-configuring-value-generation"></a>値の生成を明示的に構成する

上で説明したように、主キーの値生成は自動的に設定されていますが、キー以外のプロパティに対しても同様の処理を行うことができ EF Core。 挿入されたエンティティに対して値が生成されるようにプロパティを構成するには、次のようにします。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

同様に、追加または更新時に値が生成されるようにプロパティを構成することもできます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> 既定値または計算列とは異なり _how * 値が生成されます。これは、使用されているデータベースプロバイダーによって異なります。 データベースプロバイダーでは、一部のプロパティの型に対して自動的に値の生成が設定される場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。
>
> たとえば、SQL Server で、GUID プロパティが追加時に生成された値として構成されている場合、プロバイダーは、最適なシーケンシャル GUID 値を生成するアルゴリズムを使用して、値生成クライアント側を自動的に実行します。 ただし、DateTime プロパティにを指定しても `ValueGeneratedOnAdd()` 効果はありません ([datetime 値の生成については、以下のセクションを参照して](#datetime-value-generation)ください)。
>
> 同様に、add または update で生成されるように構成され、同時実行トークンとしてマークされている byte [] プロパティは、rowversion データ型を使用して設定されるので、データベースで値が自動的に生成されます。 ただし、を指定しても何も `ValueGeneratedOnAddOrUpdate()` 起こりません。
>
> [!NOTE]
> 使用するデータベースプロバイダーによっては、値が EF またはデータベースでクライアント側で生成される場合があります。 値がデータベースによって生成された場合、エンティティをコンテキストに追加するときに EF によって一時的な値が割り当てられることがあります。この一時値は、の間にデータベースによって生成された値に置き換えられ `SaveChanges()` ます。 詳細については、 [一時値に関するドキュメントを参照してください](xref:core/change-tracking/explicit-tracking#temporary-values)。

## <a name="datetime-value-generation"></a>日付/時刻値の生成

一般的な要求では、列が最初に挿入された日時 (追加時に値が生成されたとき)、または最終更新日 (追加または更新時に生成された値) の日付/時刻を含むデータベース列が必要です。 これを行うにはさまざまな方法がありますが、EF Core プロバイダーは、通常、日付/時刻列に対して自動的に値の生成を設定しません。これを自分で構成する必要があります。

### <a name="creation-timestamp"></a>作成タイムスタンプ

通常、行の作成タイムスタンプを持つように日付/時刻列を構成する場合は、適切な SQL 関数を使用して既定値を構成することが重要です。 たとえば SQL Server では、次のものを使用できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

必要に応じて、適切な関数を選択してください (例: `GETDATE()` vs. `GETUTCDATE()` )。

### <a name="update-timestamp"></a>タイムスタンプの更新

格納されている計算列は最後に更新されたタイムスタンプを管理するための優れたソリューションであるように見えますが、データベースでは通常、計算列になどの関数を指定することはできません `GETDATE()` 。 別の方法として、データベーストリガーを設定して同じ効果を得ることもできます。

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

トリガーの作成の詳細については、 [「移行における生の SQL の使用に関するドキュメント」を参照してください](xref:core/managing-schemas/migrations/managing#adding-raw-sql)。

## <a name="overriding-value-generation"></a>オーバーライド (値の生成を)

プロパティは値の生成用に構成されていますが、多くの場合、その値を明示的に指定することもできます。 実際に動作するかどうかは、構成されている特定の値生成メカニズムによって異なります。列の既定値を使用するのではなく、明示的な値を指定することもできますが、計算列で同じことを行うことはできません。

明示的な値を使用して値の生成をオーバーライドするには、そのプロパティの型の CLR 既定値ではない任意の値 (for、for、for、など) にプロパティを設定するだけです `null` `string` `0` `int` `Guid.Empty` `Guid` 。

> [!NOTE]
> 既定では、明示的な値を SQL Server ID に挿入しようとすると失敗します。 [回避策については、次のドキュメントを参照してください](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)。

追加または更新時に生成される値として構成されたプロパティの値を明示的に指定するには、次のようにプロパティを構成する必要もあります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>値の生成なし

前述のような特定のシナリオとは別に、プロパティには、通常、値の生成が構成されていません。つまり、データベースに保存される値を常に指定する必要があります。 この値は、コンテキストに追加する前に、新しいエンティティに割り当てる必要があります。

ただし、場合によっては、規約によって設定された値の生成を無効にすることが必要になる場合があります。 たとえば、int 型の主キーは、通常、追加時の値 (たとえば、SQL Server の id 列) として暗黙的に構成されます。 これは、次の方法で無効にできます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
