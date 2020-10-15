---
title: 基本の保存 - EF Core
description: Entity Framework Core を使用したデータの追加、更新、削除に関する基本情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: c6288fc8ca79b78b6c377f25e3874211b38059b2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063635"
---
# <a name="basic-save"></a>基本の保存

コンテキストとエンティティ クラスを使用してデータを追加、変更、および削除する方法を説明します。

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/)は GitHub で確認できます。

## <a name="adding-data"></a>データの追加

エンティティ クラスの新しいインスタンスを追加するには、*DbSet.Add* メソッドを使用します。 データは、*SaveChanges* が呼び出されたときにデータベースに挿入されます。

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> Add、Attach、および Update メソッドは、そのすべてが「[関連データ](xref:core/saving/related-data)」で説明するように、すべてのエンティティで動作します。 EntityEntry.State プロパティを使用して、単一のエンティティの状態を設定できます。 たとえば、「 `context.Entry(blog).State = EntityState.Modified` 」のように入力します。

## <a name="updating-data"></a>データの更新

EF は、コンテキストによって追跡されている既存のエンティティに対して行われた変更を自動的に検出します。 これには、データベースから読み込む/クエリするエンティティと、既に追加され、データベースに保存されているエンティティが含まれます。

プロパティに割り当てられる値を変更した後、*SaveChanges* を呼び出すだけです。

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>データの削除

エンティティ クラスのインスタンスを削除するには、*DbSet.Remove* メソッドを使用します。

エンティティが既にデータベースに存在する場合は、*SaveChanges* の処理中に削除されます。 エンティティがまだデータベースに保存されていない (追加時に追跡される) 場合は、コンテキストから削除され、*SaveChanges* が呼び出されたときにエンティティが挿入されることはなくなります。

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>1 つの SaveChanges 内の複数の操作

複数の追加/更新/削除操作を組み合わせて、*SaveChanges* の 1 回の呼び出しで使用できます。

> [!NOTE]  
> ほとんどのデータベース プロバイダーでは、*SaveChanges* はトランザクションです。 これは、すべての操作が成功するか失敗するかのいずれかであり、操作の一部のみが適用されることはないことを意味します。

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
