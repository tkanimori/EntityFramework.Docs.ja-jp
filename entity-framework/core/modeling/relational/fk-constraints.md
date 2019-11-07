---
title: Foreign Key 制約-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: df739f01a799ec8edad4cf44d8cf50edf292992f
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655994"
---
# <a name="foreign-key-constraints"></a>外部キー制約

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

外部キー制約は、モデル内のリレーションシップごとに導入されます。

## <a name="conventions"></a>規約

規則により、外部キー制約には `FK_<dependent type name>_<principal type name>_<foreign key property name>`という名前が付けられます。 複合外部キーの場合 `<foreign key property name>` は、外部キープロパティ名のアンダースコア区切りの一覧になります。

## <a name="data-annotations"></a>データの注釈

外部キー制約の名前は、データ注釈を使用して構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、リレーションシップの外部キー制約の名前を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
