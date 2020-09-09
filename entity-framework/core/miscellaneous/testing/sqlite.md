---
title: SQLite を使用したテスト-EF Core
description: SQLite を使用した Entity Framework Core アプリケーションのテスト
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: b580ed58cb9466c8eac32f71951734f4bd565733
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617698"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>SQLite を使用した EF Core アプリケーションのテスト

> [!WARNING]
> SQLite を使用すると、EF Core アプリケーションをテストする効果的な方法になります。
> ただし、SQLite の動作が他のデータベースシステムと異なる場合は、問題が発生する可能性があります。 問題とトレードオフの詳細については、「 [EF Core を使用したコードのテスト](xref:core/miscellaneous/testing/index) 」を参照してください。  

このドキュメントでは、 [EF Core を使用するアプリケーションのテスト方法を示すサンプル](xref:core/miscellaneous/testing/testing-sample)で導入された概念について説明します。
このサンプルのコード例を次に示します。

## <a name="using-sqlite-in-memory-databases"></a>SQLite インメモリデータベースの使用

通常、SQLite は単純なファイルとしてデータベースを作成し、アプリケーションでインプロセスのファイルにアクセスします。
これは、特に高速 [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)を使用する場合には非常に高速です。 

SQLite では、単にメモリ内で作成されたデータベースを使用することもできます。
これは、インメモリデータベースの有効期間を理解していれば、EF Core で簡単に使用できます。
* データベースへの接続が開かれると、データベースが作成されます。
* データベースへの接続が閉じられると、データベースは削除されます。

指定されている場合、既に開いている接続が使用されるため、EF Core は閉じようとしません。
そのため、メモリ内の SQLite データベースで EF Core を使用するには、EF に渡す前に接続を開く必要があります。  

この [サンプル](xref:core/miscellaneous/testing/testing-sample) では、次のコードを使用してこれを実現します。

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

注意:
* メソッドは、 `CreateInMemoryDatabase` SQLite メモリ内データベースを作成し、そのデータベースへの接続を開きます。
* 作成されたはから抽出され、 `DbConnection` `ContextOptions` 保存されます。
* リソースがリークしないようにテストが破棄されると、接続は破棄されます。 

> [!NOTE]
> [問題 #16103](https://github.com/dotnet/efcore/issues/16103) は、この接続管理を容易にする方法を追跡することです。 
