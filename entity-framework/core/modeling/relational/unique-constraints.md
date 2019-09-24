---
title: 代替キー (Unique 制約)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197607"
---
# <a name="alternate-keys-unique-constraints"></a>代替キー (一意の制約)

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

一意の制約は、モデル内の各代替キーに対して導入されます。

## <a name="conventions"></a>規約

慣例により、代替キーに対して導入されたインデックスと制約に`AK_<type name>_<property name>`はという名前が付けられます。 複合キー `<property name>`の場合は、アンダースコアで区切られたプロパティ名のリストになります。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して Unique 制約を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、代替キーのインデックスと制約の名前を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
