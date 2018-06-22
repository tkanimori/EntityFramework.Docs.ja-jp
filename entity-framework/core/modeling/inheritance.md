---
title: 継承の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
ms.technology: entity-framework-core
uid: core/modeling/inheritance
ms.openlocfilehash: f0394bc55dfbfea8277b1ddf898361165dd1fe51
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052782"
---
# <a name="inheritance"></a>継承

EF モデルでの継承を使用すると、データベース内のエンティティ クラスの継承の表現方法を制御します。

## <a name="conventions"></a>規則

慣例により、データベースでの継承の表現方法を決定する、データベース プロバイダーの責任です。 参照してください[(リレーショナル データベース) の継承](relational/inheritance.md)のリレーショナル データベース プロバイダーで処理する方法です。

モデルで、2 つまたは複数の継承された型が明示的に含まれている場合、EF は継承設定のみです。 EF は基本データ型または派生型それ以外の場合、モデルに含まれていないことをスキャンしません。 公開することで、モデル内の型を含めることができます、 *DbSet<TEntity>* 継承階層の種類ごとにします。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

公開するたくない場合、 *DbSet<TEntity>* 階層内の 1 つまたは複数のエンティティ、モデルに含まれていることを確認する Fluent API を使用することができます。
規則に依存しないようにする場合は、明示的に使用して、基本型を指定できますと`HasBaseType`です。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 使用することができます`.HasBaseType((Type)null)`階層からエンティティの種類を削除します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

継承の Fluent API を使用しているデータベース プロバイダーによって異なります。 参照してください[(リレーショナル データベース) の継承](relational/inheritance.md)のリレーショナル データベース プロバイダーに対して実行できる構成します。
