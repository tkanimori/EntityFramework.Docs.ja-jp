---
title: 代替キー (一意制約) - EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
ms.technology: entity-framework-core
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 1b7e2bef6ede95f8c27211ba00dcc6b97cccde9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052792"
---
# <a name="alternate-keys-unique-constraints"></a>代替キー (一意制約)

> [!NOTE]  
> このセクションの構成は、一般にリレーショナル データベースに適用されます。 ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。

Unique 制約は、モデル内の代替キーごとに導入されました。

## <a name="conventions"></a>規則

慣例により、インデックスと代替キーの導入された制約の名前は`AK_<type name>_<property name>`します。 複合の代替キーの`<property name>`プロパティ名のアンダー スコアの区切られたリストになります。

## <a name="data-annotations"></a>データの注釈

Unique 制約は、データ注釈を使用していない構成できます。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、代替キーのインデックスと制約名を構成することができます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
