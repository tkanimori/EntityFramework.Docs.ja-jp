---
title: 基本の保存 - EF Core
description: Entity Framework Core を使用したデータの追加、更新、削除に関する基本情報
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: ea73ff86d8843acf8226f9d6696b79b99da9dab7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070953"
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
