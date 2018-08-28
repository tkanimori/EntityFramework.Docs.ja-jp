---
title: 代替キー (一意制約) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7ec58ee31aac79e15329dc8542f37fd117772fbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994192"
---
# <a name="alternate-keys-unique-constraints"></a>代替キー (一意制約)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

Unique 制約には、モデル内の各代替キーが導入されました。

## <a name="conventions"></a>規約

慣例により、インデックスと代替キー用に導入された制約の名前は`AK_<type name>_<property name>`します。 複合の代替キーの`<property name>`はプロパティ名、アンダー スコア区切りリストになります。

## <a name="data-annotations"></a>データの注釈

Unique 制約は、データ注釈を使用していない構成できます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、代替キーのインデックスと制約名を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
