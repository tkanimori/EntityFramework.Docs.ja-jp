---
title: 代替キー-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: e5bb0602adb465435c8e88d045395a9424b2d9a3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655772"
---
# <a name="alternate-keys"></a>代替キー

代替キーは、主キーに加えて、各エンティティインスタンスの代替一意識別子として機能します。 代替キーは、リレーションシップのターゲットとして使用できます。 リレーショナルデータベースを使用する場合、これは代替キー列の一意のインデックス/制約の概念と、その列を参照する1つ以上の foreign key 制約にマップされます。

> [!TIP]  
> 列の一意性を確保するだけの場合は、別のキーではなく一意のインデックスを作成するには、「[インデックス](indexes.md)」を参照してください。 EF では、代替キーは外部キーのターゲットとして使用できるため、一意のインデックスよりも多くの機能を提供します。

通常、代替キーは必要に応じて導入され、手動で構成する必要はありません。 詳細については、「[規約](#conventions)」を参照してください。

## <a name="conventions"></a>規約

慣例により、主キーではないプロパティをリレーションシップのターゲットとして指定すると、代替キーが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して代替キーを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、1つのプロパティを代替キーとして構成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=7,8)]

また、Fluent API を使用して、複数のプロパティを別のキー (複合代替キーと呼ばれます) として構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=7,8)]
