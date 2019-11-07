---
title: 既定値-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: b6ac283d551e2c6ee119f7de6933363b5d8793a1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655905"
---
# <a name="default-values"></a>既定値

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

列の既定値は、新しい行が挿入されても列に値が指定されていない場合に挿入される値です。

## <a name="conventions"></a>規約

慣例により、既定値は構成されていません。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して既定値を設定することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティの既定値を指定できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValue.cs?name=DefaultValue&highlight=9)]

既定値の計算に使用される SQL フラグメントを指定することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValueSql.cs?name=DefaultValueSql&highlight=9)]
