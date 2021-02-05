---
title: インデックス-EF Core
description: Entity Framework Core モデルでのインデックスの構成
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "98128538"
---
# <a name="indexes"></a>Indexes

インデックスは、多くのデータストアで共通の概念です。 データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。 適切なインデックスの使用方法の詳細については、パフォーマンスドキュメントの「 [インデックス」セクション](xref:core/performance/efficient-querying#use-indexes-properly) を参照してください。

列に対してインデックスを指定するには、次のようにします。

## <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> データ注釈を使用したインデックスの構成は、EF Core 5.0 で導入されました。

## <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> 規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。
>
> EF Core では、個別のプロパティセットごとに1つのインデックスのみがサポートされます。 規則または以前の構成によって既にインデックスが定義されている一連のプロパティにインデックスを構成する場合は、そのインデックスの定義を変更します。 これは、規則によって作成されたインデックスをさらに構成する場合に便利です。

## <a name="composite-index"></a>複合インデックス

インデックスは、複数の列にまたがることもできます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

***

複数の列に対するインデックス ( *複合インデックス* とも呼ばれます) は、インデックスの列をフィルター処理するクエリを高速化しますが、インデックスの対象となる *最初* の列に対してのみフィルター処理を行うクエリも行います。 詳細については、パフォーマンスに関する [ドキュメント](xref:core/performance/efficient-querying#use-indexes-properly) を参照してください。

## <a name="index-uniqueness"></a>インデックスの一意性

既定では、インデックスは一意ではありません。複数の行がインデックスの列セットに対して同じ値を持つことができます。 次のように、インデックスを一意にすることができます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

インデックスの列セットに同じ値を持つ複数のエンティティを挿入しようとすると、例外がスローされます。

## <a name="index-name"></a>インデックス名

慣例により、リレーショナルデータベースに作成されたインデックスにはという名前が付けられ `IX_<type name>_<property name>` ます。 複合インデックスの場合、は、 `<property name>` アンダースコアで区切られたプロパティ名のリストになります。

次のように、データベースに作成されたインデックスの名前を設定できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a>インデックスフィルター

一部のリレーショナルデータベースでは、フィルター処理されたインデックスまたは部分的なインデックスを指定できます。 これにより、列の値のサブセットにのみインデックスを作成し、インデックスのサイズを小さくして、パフォーマンスとディスク領域の使用率を向上させることができます。 フィルター選択されたインデックス SQL Server の詳細については、 [のドキュメントを参照してください](/sql/relational-databases/indexes/create-filtered-indexes)。

Fluent API を使用して、SQL 式として指定されたインデックスにフィルターを指定できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

SQL Server プロバイダー EF を使用すると、 `'IS NOT NULL'` 一意のインデックスの一部である null 許容型のすべての列に対してフィルターが追加されます。 この規則をオーバーライドするには、 `null` 値を指定します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>[付加列]

一部のリレーショナルデータベースでは、インデックスに含まれるが、"キー" の一部ではない列のセットを構成できます。 これにより、テーブル自体にアクセスする必要がないため、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合、クエリのパフォーマンスが大幅に向上します。 付加列 SQL Server の詳細については、 [のドキュメントを参照してください](/sql/relational-databases/indexes/create-indexes-with-included-columns)。

次の例では、 `Url` 列がインデックスキーの一部であるため、その列に対するクエリフィルター処理でインデックスを使用できます。 さらに、 `Title` 列と列にのみアクセス `PublishedOn` するクエリは、テーブルにアクセスする必要がなく、より効率的に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
