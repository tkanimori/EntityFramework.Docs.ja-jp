---
title: "基本的なクエリの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ab6e35f1-397f-41c0-9ef4-85aec5466377
ms.technology: entity-framework-core
uid: core/querying/basic
ms.openlocfilehash: 5070faf2aeeffad680e24e7de5a0ffa03a8f0064
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="basic-queries"></a>基本的なクエリ

言語統合クエリ (LINQ) を使用して、データベースからエンティティを読み込む方法を説明します。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。

## <a name="101-linq-samples"></a>101 個の LINQ サンプル

このページは、Entity Framework のコアで一般的なタスクを実現するために、いくつかの例を示します。 LINQ で何ができるかを示すサンプルの広範囲に、次を参照してください。 [101 個の LINQ サンプル](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b)です。

## <a name="loading-all-data"></a>すべてのデータを読み込み

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}
```

## <a name="loading-a-single-entity"></a>1 つのエンティティの読み込み

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Single(b => b.BlogId == 1);
}
```

## <a name="filtering"></a>フィルター処理

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Where(b => b.Url.Contains("dotnet"))
        .ToList();
}
```
