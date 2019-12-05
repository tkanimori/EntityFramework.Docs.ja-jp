---
title: インデックス (リレーショナルデータベース)-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/modeling/relational/indexes
ms.openlocfilehash: e14615275f85ee9b6b32d080905465d33963feca
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824577"
---
# <a name="indexes-relational-database"></a>インデックス (リレーショナルデータベース)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

リレーショナルデータベースのインデックスは、Entity Framework の中核となるインデックスと同じ概念にマップされます。

## <a name="conventions"></a>規約

規則により、インデックスには `IX_<type name>_<property name>`という名前が付けられます。 複合インデックスの場合 `<property name>` は、アンダースコアで区切られたプロパティ名のリストになります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、インデックスの名前を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

フィルターを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

SQL Server プロバイダー EF を使用すると、一意のインデックスの一部である null 許容型のすべての列に対して `'IS NOT NULL'` フィルターが追加されます。 この規則をオーバーライドするには、`null` 値を指定します。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a>SQL Server インデックスに列を含める

[付加列を使用](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)してインデックスを構成すると、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合に、クエリのパフォーマンスを大幅に向上させることができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexInclude.cs?name=Model)]
