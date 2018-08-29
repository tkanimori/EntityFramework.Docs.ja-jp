---
title: インデックス - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 605b30ce710d9034deab97f695496ec66a576565
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993218"
---
# <a name="indexes"></a>インデックス

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

リレーショナル データベース内のインデックスは、Entity framework core でのインデックスと同じ概念にマップされます。

## <a name="conventions"></a>規約

慣例により、インデックスの名前は`IX_<type name>_<property name>`します。 複合インデックスの`<property name>`はプロパティ名、アンダー スコア区切りリストになります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してインデックスを構成しないことができます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用すると、インデックスの名前を構成します。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

フィルターを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

SQL Server プロバイダー EF を使用して追加するときは、一意のインデックスの一部であるすべての null 許容列の ' IS NOT NULL' フィルター処理します。 この規則を指定することができますを上書きする、`null`値。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
