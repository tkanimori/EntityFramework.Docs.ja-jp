---
title: 継承 (リレーショナルデータベース)-EF Core
description: Entity Framework Core を使用してリレーショナルデータベースでエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 30e25aa2968ceab03404baddb46e0ae59fc3ea6b
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824746"
---
# <a name="inheritance-relational-database"></a>継承 (リレーショナル データベース)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

EF モデルの継承は、エンティティクラスの継承がデータベースでどのように表現されるかを制御するために使用されます。

> [!NOTE]  
> 現時点では、EF Core で実装されているのは、階層単位 (TPH) パターンだけです。 テーブルごとのテーブル (TPT) や具象型ごとのテーブル (TPC) などのその他の一般的なパターンは、まだ使用できません。

## <a name="conventions"></a>規約

既定では、継承は、階層単位 (TPH) パターンを使用してマップされます。 TPH は、1つのテーブルを使用して、階層内のすべての型のデータを格納します。 識別子列は、各行が表す型を識別するために使用されます。

EF Core は、継承された複数の型がモデルに明示的に含まれている場合にのみ継承を設定します (詳細については、「[継承](../inheritance.md)」を参照してください)。

単純な継承シナリオと、TPH パターンを使用してリレーショナルデータベーステーブルに格納されているデータを示す例を次に示します。 *識別子*列には、各行に格納されている*ブログ*の種類が示されます。

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![イメージ](_static/inheritance-tph-data.png)

>[!NOTE]
> TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して継承を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、識別子列の名前と型、および階層内の各型を識別するために使用される値を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a>識別子プロパティの構成

上の例では、識別子は、階層の基本エンティティの[shadow プロパティ](xref:core/modeling/shadow-properties)として作成されます。 モデルのプロパティであるため、他のプロパティと同様に構成できます。 たとえば、既定の規則に従った識別子を使用する場合の最大長を設定するには、次のようにします。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/DefaultDiscriminator.cs#DiscriminatorConfiguration)]

識別子は、エンティティ内の .NET プロパティにマップして構成することもできます。 例:

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs#NonShadowDiscriminator)]

## <a name="shared-columns"></a>共有列

2つの兄弟エンティティ型に同じ名前のプロパティがある場合、既定では2つの異なる列にマップされます。 ただし、互換性がある場合は、同じ列にマップできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs#SharedTPHColumns)]