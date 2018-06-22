---
title: インデックスの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: f577fccfefc6908edf2ac47ae630323d7a9f5f2b
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29679000"
---
# <a name="indexes"></a>インデックス

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

リレーショナル データベース内のインデックスは、Entity Framework のコアでのインデックスと同じ概念にマップされます。

## <a name="conventions"></a>規約

慣例により、インデックスが名前付き`IX_<type name>_<property name>`します。 複合インデックスの`<property name>`プロパティ名のアンダー スコアの区切られたリストになります。

## <a name="data-annotations"></a>データの注釈

インデックスは、データ注釈を使用していない構成できます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用すると、インデックスの名前を構成します。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

フィルターを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

SQL Server プロバイダー EF を使用して追加したときに、' は NOT NULL' は一意のインデックスの一部であるすべての null 許容列のフィルター処理します。 この規則を指定することができますを上書きする、`null`値。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
