---
title: InMemory - EF Core でのテスト
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 8aaea52f22954ef6a2b7d9b9c5627597c61ac644
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562547"
---
# <a name="testing-with-inmemory"></a>InMemory のテスト

InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドがなく、実際のデータベースへの接続を近似するものを使用してコンポーネントをテストする場合に便利です。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub で確認できます。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory はリレーショナル データベースではありません。

EF Core データベース プロバイダーはリレーショナル データベースではありません。 InMemory ように、テスト用の汎用データベースを設計し、リレーショナル データベースを模倣するためのものではありません。

次のとおりのいくつかの例では:

* InMemory はリレーショナル データベースの参照整合性制約に違反するデータを保存できます。
* モデルのプロパティを DefaultValueSql(string) を使用する場合は、これは、リレーショナル データベース API であり、効果はありません InMemory に対して実行したとき。
* [タイムスタンプや行のバージョンを使用して同時実行](xref:core/modeling/concurrency#timestamprow-version)(`[Timestamp]`または`IsRowVersion`) はサポートされていません。 いいえ[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)更新が行われた場合にスローされます古いの同時実行トークンを使用します。

> [!TIP]  
> 多くのテスト目的でのこれらの違いは関係ありません。 ただし、真のリレーショナル データベースのように動作するものをテストする場合は、使用を検討して[SQLite メモリ内モード](sqlite.md)します。

## <a name="example-testing-scenario"></a>テスト シナリオの例

ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。 内部的には、 `DbContext` SQL Server データベースに接続します。 ように、コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できます InMemory データベースに接続するには、このコンテキストを交換する便利なことが、コンテキストの二重。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストを準備します。

### <a name="avoid-configuring-two-database-providers"></a>2 つのデータベース プロバイダーを構成しない場合

テストでは、InMemory プロバイダーを使用するコンテキストの外部で構成すること。 オーバーライドすることで、データベース プロバイダーを構成している場合`OnConfiguring`コンテキストでする必要がある 1 つ既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> ASP.NET Core を使用している場合、必要はありませんこのコードは、データベース プロバイダーが既に (Startup.cs) のコンテキスト外で構成されているためです。

### <a name="add-a-constructor-for-testing"></a>テストのコンス トラクターを追加します。

別のデータベースに対するテストを有効にする最も簡単な方法が変更のコンテキストを受け取るコンス トラクターを公開するには、`DbContextOptions<TContext>`します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` すべてに接続するデータベースなど、その設定のコンテキストに指示します。 これは、コンテキストの OnConfiguring メソッドを使用して構築された同じオブジェクトです。

## <a name="writing-tests"></a>テストの記述

このプロバイダーでのテストにキーをメモリ内データベースのスコープを制御、InMemory プロバイダーを使用してコンテキストを通知する機能があります。 通常、クリーンなデータベースは、各テスト メソッドにします。

InMemory データベースを使用するテスト クラスの例を次に示します。 各テスト メソッドでは、一意のデータベース名、つまり各メソッドが、独自の InMemory データベースを指定します。

>[!TIP]
> `.UseInMemoryDatabase()`を利用するには、NuGet パッケージの拡張メソッドで[Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)を参照してください。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
