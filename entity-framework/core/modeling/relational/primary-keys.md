---
title: 主キー-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656050"
---
# <a name="primary-keys"></a>主キー

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

主キー制約は、各エンティティ型のキーに対して導入されています。

## <a name="conventions"></a>規約

慣例として、データベースの主キーには `PK_<type name>`という名前が付けられます。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、主キーのリレーショナルデータベース固有の側面を構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、データベース内の primary key 制約の名前を構成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
