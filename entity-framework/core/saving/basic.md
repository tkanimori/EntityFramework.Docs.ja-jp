---
title: "基本的な保存の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: e99d755b8f7c42b15a73cfdb6a2f8999a405a739
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="basic-save"></a>基本の保存

追加、変更、および、コンテキストおよびエンティティ クラスを使用してデータを削除する方法を説明します。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/)GitHub でします。

## <a name="adding-data"></a>データの追加

使用して、 *DbSet.Add*エンティティ クラスの新しいインスタンスを追加するメソッド。 データを呼び出すと、データベース内に挿入される*SaveChanges*です。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

## <a name="updating-data"></a>データの更新

EF は、コンテキストによって追跡されている既存のエンティティに行われた変更を自動的に検出されます。 これには、エンティティをデータベースから読み込み/クエリして以前に追加され、データベースに保存されているエンティティが含まれます。

プロパティに割り当てられた値を単に変更し、呼び出す*SaveChanges*です。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>データの削除

使用して、 *DbSet.Remove*するエンティティ クラスのインスタンスを削除するメソッド。

エンティティがデータベースに既に存在する場合、中には削除されます*SaveChanges*です。 エンティティが (つまり追跡追加されると)、データベースに保存されていない場合それがコンテキストから削除されますあり、使用できなくなります挿入時に*SaveChanges*と呼びます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>1 つの SaveChanges で複数の操作

1 回の呼び出しに複数の追加/更新/削除操作を組み合わせることができます*SaveChanges*です。

> [!NOTE]  
> ほとんどのデータベース プロバイダーの*SaveChanges*がトランザクションです。 つまり、すべての操作の成功または失敗が操作しない左部分的に適用されます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
