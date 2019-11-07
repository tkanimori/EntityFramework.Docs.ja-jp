---
title: 既定のスキーマ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 1579fed007997aa4cf49b4c1290aee86c81c0000
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655968"
---
# <a name="default-schema"></a>既定のスキーマ

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

既定のスキーマは、そのオブジェクトに対してスキーマが明示的に構成されていない場合にオブジェクトが作成されるデータベーススキーマです。

## <a name="conventions"></a>規約

慣例により、データベースプロバイダーは最も適切な既定のスキーマを選択します。 たとえば、Microsoft SQL Server では `dbo` スキーマが使用され、SQLite ではスキーマが使用されません (スキーマは SQLite ではサポートされていないため)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して既定のスキーマを設定することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、既定のスキーマを指定できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultSchema.cs?name=DefaultSchema&highlight=7)]
