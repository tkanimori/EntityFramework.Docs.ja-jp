---
title: SQLite - EF Core でのテスト
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 8fcb4b1a37da6f52219ebe3c672160627fe28fab
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052702"
---
# <a name="testing-with-sqlite"></a>SQLite でテストします。

SQLite は、SQLite を使用して実際のデータベース操作のオーバーヘッドがなく、リレーショナル データベースに対してテストを記述できるようにする、インメモリ モードです。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)github

## <a name="example-testing-scenario"></a>テスト シナリオの例

ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。 内部的に使用して、 `DbContext` SQL Server データベースに接続します。 コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できるようにする、インメモリ SQLite データベースに接続するには、このコンテキストを交換すると便利だコンテキストの二重です。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストを整える

### <a name="avoid-configuring-two-database-providers"></a>2 つのデータベース プロバイダーを構成しません。

テストにしようとする InMemory プロバイダーを使用するコンテキストの外部で構成します。 オーバーライドすることで、データベース プロバイダーを構成する場合`OnConfiguring`コンテキストにする必要がいずれかが既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。

> [!TIP]  
> ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが構成されているためです。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>テストするためのコンス トラクターを追加します。

別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを受け取るコンス トラクターを公開するを変更する、`DbContextOptions<TContext>`です。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>`すべてに接続するデータベースなど、その設定のコンテキストに指示します。 これは、同じオブジェクトのコンテキストで OnConfiguring メソッドを実行して組み込まれています。

## <a name="writing-tests"></a>テストの記述

このプロバイダーでのテストに、キーは、SQLite を使用して、メモリ内のデータベースのスコープを制御するコンテキストを確認する機能です。 データベースのスコープは、接続の開閉によって制御されます。 データベースは、接続が開いている間に制限されます。 通常、クリーンなデータベースは各テスト メソッドにします。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
