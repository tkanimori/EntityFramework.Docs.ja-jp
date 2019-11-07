---
title: '& 型を除外する (EF Core)'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: 1249e71c02e58afe7fe06b3fdcf523dfa0c9b17c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655741"
---
# <a name="including--excluding-types"></a>種類を含める/除外する

モデルに型を含めると、EF にはその型に関するメタデータが含まれ、データベースとの間でインスタンスの読み取りおよび書き込みが試行されます。

## <a name="conventions"></a>規約

慣例により、コンテキストの `DbSet` プロパティで公開される型は、モデルに含まれます。 さらに、`OnModelCreating` メソッドに記述されている型も含まれます。 最後に、検出された型のナビゲーションプロパティを再帰的に調べることによって検出された型も、モデルに含まれます。

**たとえば、次のコードでは、3種類すべてが検出されています。**

* コンテキストの `DbSet` プロパティで公開されているため `Blog`

* `Blog.Posts` ナビゲーションプロパティを使用して検出されるため `Post`

* `AuditEntry` は `OnModelCreating` に記載されています。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/IncludedTypes.cs?name=IncludedTypes&highlight=3,7,16)]

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、モデルから型を除外できます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデルから型を除外できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?highlight=12)]
