---
title: 継承 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: f6b5c8f5a398ac1e28e29bc17f0674c5b76d7b20
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319128"
---
# <a name="inheritance"></a>継承

EF モデルでの継承は、データベース内でエンティティ クラスの継承を表現する方法を制御するために使用されます。

## <a name="conventions"></a>規約

慣例により、データベース内の継承の表示方法を指定するデータベース プロバイダーの責任です。 参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)のリレーショナル データベース プロバイダーで処理する方法。

2 つまたは複数の継承された型がモデルで明示的に含まれている場合、EF は継承設定のみ。 それ以外の場合、モデルに含まれていたいない基本または派生型の場合は、EF をスキャンしません。 公開することで、モデルの型を含めることができます、 *DbSet<TEntity>* 継承階層の種類ごとにします。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

公開するたくない場合、 *DbSet<TEntity>* 階層の 1 つまたは複数のエンティティに対して、モデルに含まれていることを確認する、Fluent API を使用することができます。
規則に依存しない場合は、明示的に使用する基本型を指定できますと`HasBaseType`します。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 使用することができます`.HasBaseType((Type)null)`エンティティ型を階層から削除します。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

継承の Fluent API を使用するデータベース プロバイダーによって異なります。 参照してください[継承 (リレーショナル データベース)](relational/inheritance.md)の構成のリレーショナル データベース プロバイダーを実行できます。
