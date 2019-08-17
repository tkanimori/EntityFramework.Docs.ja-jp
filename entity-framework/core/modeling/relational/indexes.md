---
title: インデックス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: dfada7446f812f3c277572cc1338441272e8f448
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565358"
---
# <a name="indexes"></a>インデックス

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

リレーショナルデータベースのインデックスは、Entity Framework の中核となるインデックスと同じ概念にマップされます。

## <a name="conventions"></a>規約

規則により、インデックスに`IX_<type name>_<property name>`はという名前が付けられます。 複合インデックス`<property name>`の場合は、アンダースコアで区切られたプロパティ名のリストになります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、インデックスの名前を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

フィルターを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

SQL Server プロバイダー EF を使用すると、一意インデックスの一部である null 許容型のすべての列に対して ' IS NOT NULL ' フィルターが追加されます。 この規則をオーバーライドするには、 `null`値を指定します。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a>SQL Server インデックスに列を含める

[付加列を使用](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)してインデックスを構成すると、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合に、クエリのパフォーマンスを大幅に向上させることができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/ForSqlServerHasIndex.cs?name=Model)]
