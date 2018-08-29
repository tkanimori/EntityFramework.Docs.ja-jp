---
title: 基本的なクエリ - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ab6e35f1-397f-41c0-9ef4-85aec5466377
ms.technology: entity-framework-core
uid: core/querying/basic
ms.openlocfilehash: eceac81546b23157611edd530b8b71f71e970c1f
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "42447750"
---
# <a name="basic-queries"></a>基本的なクエリ

言語統合クエリ (LINQ) を使用して、データベースからエンティティを読み込む方法を説明します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="101-linq-samples"></a>101 個の LINQ サンプル

このページでは、Entity Framework Core で一般的なタスクを実現するいくつかの例を示します。 LINQ で何ができるかを示す広範囲なサンプルについては、「[101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b)」(101 個の LINQ サンプル) 参照してください。

## <a name="loading-all-data"></a>すべてのデータの読み込み

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}
```

## <a name="loading-a-single-entity"></a>単一のエンティティの読み込み

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
