---
title: SQLite - EF Core でのテスト
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: bc9d6768a90ce17160c4126d2a68fddaa30d63de
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996869"
---
# <a name="testing-with-sqlite"></a>SQLite のテスト

SQLite は、SQLite を使用して、実際のデータベース操作のオーバーヘッドがなく、リレーショナル データベースに対してテストを記述することができるようにする、インメモリ モードです。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)github

## <a name="example-testing-scenario"></a>テスト シナリオの例

ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。 内部的には、 `DbContext` SQL Server データベースに接続します。 ように、コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できます、メモリ内の SQLite データベースに接続するには、このコンテキストを交換する便利なことが、コンテキストの二重。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストを準備します。

### <a name="avoid-configuring-two-database-providers"></a>2 つのデータベース プロバイダーを構成しない場合

テストでは、InMemory プロバイダーを使用するコンテキストの外部で構成すること。 オーバーライドすることで、データベース プロバイダーを構成している場合`OnConfiguring`コンテキストでする必要がある 1 つ既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。

> [!TIP]  
> ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが構成されているためです。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>テストのコンス トラクターを追加します。

別のデータベースに対するテストを有効にする最も簡単な方法が変更のコンテキストを受け取るコンス トラクターを公開するには、`DbContextOptions<TContext>`します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` すべてに接続するデータベースなど、その設定のコンテキストに指示します。 これは、コンテキストの OnConfiguring メソッドを使用して構築された同じオブジェクトです。

## <a name="writing-tests"></a>テストの記述

このプロバイダーでのテストにキーをメモリ内データベースのスコープを制御、SQLite を使用してコンテキストを通知する機能があります。 データベースのスコープは、接続の開閉によって制御されます。 データベースは、接続が開かれている期間に制限されます。 通常、クリーンなデータベースは、各テスト メソッドにします。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
