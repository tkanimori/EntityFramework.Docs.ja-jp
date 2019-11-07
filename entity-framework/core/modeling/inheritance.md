---
title: 継承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: abc1caa4d3839b7cdb52b316bcfc8f648b609b70
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655686"
---
# <a name="inheritance"></a>継承

EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。

## <a name="conventions"></a>規約

慣例により、データベースでの継承の表現方法は、データベースプロバイダーによって決まります。 リレーショナルデータベースプロバイダーでの処理方法については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。

EF では、2つ以上の継承型がモデルに明示的に含まれている場合にのみ継承が設定されます。 EF では、モデルに含まれていない基本型または派生型はスキャンされません。 継承階層の各型に対して*Dbset\<TEntity >* を公開することにより、モデルに型を含めることができます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

階層内の1つ以上のエンティティに対して*Dbset\<TEntity >* を公開しない場合は、Fluent API を使用してモデルに含まれていることを確認できます。
また、規則に依存しない場合は、`HasBaseType`を使用して基本型を明示的に指定できます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> `.HasBaseType((Type)null)` を使用すると、階層からエンティティ型を削除できます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

継承用の Fluent API は、使用しているデータベースプロバイダーによって異なります。 リレーショナルデータベースプロバイダーに対して実行できる構成については、「[継承 (リレーショナルデータベース)](relational/inheritance.md) 」を参照してください。
