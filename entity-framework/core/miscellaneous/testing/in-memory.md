---
title: "InMemory - EF Core でのテスト"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: c5c48c575e9fd693d1f28d1a6d10eb83ebbc9d70
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="testing-with-inmemory"></a>InMemory でテストします。

InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドがなく、実際のデータベースへの接続を概算するためのものを使用してコンポーネントをテストする場合に便利です。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)GitHub でします。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory はリレーショナル データベースではありません。

EF コア データベース プロバイダーはリレーショナル データベースではありません。 InMemory テスト、汎用データベースであるように設計されたされており、リレーショナル データベースを模倣するためにします。

この以下の例をいくつか:
* InMemory を使用すると、リレーショナル データベースの参照整合性制約に違反するデータを保存できます。

* DefaultValueSql(string) プロパティのモデル内で使用する場合は、これはリレーショナル データベース API であり、効果はありません InMemory に対して実行する場合。

> [!TIP]  
> 多くのテスト目的でのこれらの相違点は関係ありません。 ただし、場合は true。 リレーショナル データベースのように動作しているものに対してテストする場合は、使用を検討して[SQLite インメモリ モード](sqlite.md)です。

## <a name="example-testing-scenario"></a>テスト シナリオの例

ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。 内部的に使用して、 `DbContext` SQL Server データベースに接続します。 データベースに接続する InMemory おコードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できるようにするには、このコンテキストを交換すると便利だコンテキストの二重です。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストを整える

### <a name="avoid-configuring-two-database-providers"></a>2 つのデータベース プロバイダーを構成しません。

テストにしようとする InMemory プロバイダーを使用するコンテキストの外部で構成します。 オーバーライドすることで、データベース プロバイダーを構成する場合`OnConfiguring`コンテキストにする必要がいずれかが既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが既に構成されているためです。

### <a name="add-a-constructor-for-testing"></a>テストするためのコンス トラクターを追加します。

別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを受け取るコンス トラクターを公開するを変更する、`DbContextOptions<TContext>`です。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>`すべてに接続するデータベースなど、その設定のコンテキストに指示します。 これは、同じオブジェクトのコンテキストで OnConfiguring メソッドを実行して組み込まれています。

## <a name="writing-tests"></a>テストの記述

このプロバイダーでのテストに、キーは、InMemory プロバイダーを使用して、メモリ内のデータベースのスコープを制御するコンテキストを確認する機能です。 通常、クリーンなデータベースは各テスト メソッドにします。

InMemory データベースを使用するテスト クラスの例を次に示します。 各テスト メソッドでは、各方法に独自の InMemory データベースつまり、一意のデータベース名を指定します。

>[!TIP]
> 使用する、 `.UseInMemoryDatabase()` Nuget パッケージの参照の拡張メソッドで`Microsoft.EntityFrameworkCore.InMemory`です。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
