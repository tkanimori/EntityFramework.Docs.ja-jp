---
title: InMemory を使用したテスト-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 18641677098c20d9172136b07868dcb647d189c6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414027"
---
# <a name="testing-with-inmemory"></a>InMemory のテスト

InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドを発生させることなく、実際のデータベースへの接続を使用してコンポーネントをテストする場合に便利です。

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub で確認できます。

## <a name="inmemory-is-not-a-relational-database"></a>InMemory はリレーショナルデータベースではありません

EF Core データベースプロバイダーは、リレーショナルデータベースである必要はありません。 InMemory は、テスト用の汎用データベースとして設計されており、リレーショナルデータベースを模倣するように設計されていません。

たとえば、次のような例があります。

* InMemory を使用すると、リレーショナルデータベースの参照整合性制約に違反するデータを保存できます。
* モデルのプロパティに DefaultValueSql (string) を使用した場合、これはリレーショナルデータベース API であり、InMemory に対して実行しても効果はありません。
* [タイムスタンプ/行バージョン](xref:core/modeling/concurrency#timestamprowversion)(`[Timestamp]` または `IsRowVersion`) による同時実行はサポートされていません。 古い同時実行トークンを使用して更新が行われた場合、 [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)はスローされません。

> [!TIP]  
> 多くのテスト目的では、これらの違いは関係ありません。 ただし、真のリレーショナルデータベースのように動作するものに対してテストする場合は、 [SQLite インメモリモード](sqlite.md)の使用を検討してください。

## <a name="example-testing-scenario"></a>テストシナリオの例

アプリケーションコードがブログに関連するいくつかの操作を実行できるようにするには、次のサービスを検討してください。 内部的には、SQL Server データベースに接続する `DbContext` を使用します。 このコンテキストをスワップして、InMemory データベースに接続すると、コードを変更しなくても、このサービスに対して効率的なテストを作成できるようになります。また、コンテキストのテスト double を作成するために大量の作業を行うこともできます。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストの準備

### <a name="avoid-configuring-two-database-providers"></a>2つのデータベースプロバイダーの構成を回避する

テストでは、InMemory プロバイダーを使用するようにコンテキストを外部で構成しようとしています。 コンテキストで `OnConfiguring` をオーバーライドしてデータベースプロバイダーを構成する場合は、データベースプロバイダーがまだ構成されていない場合にのみ構成するように、いくつかの条件付きコードを追加する必要があります。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> ASP.NET Core を使用している場合、このコードは必要ありません。これは、データベースプロバイダーがコンテキストの外部 (Startup.cs) で既に構成されているためです。

### <a name="add-a-constructor-for-testing"></a>テスト用のコンストラクターを追加する

別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを変更して、`DbContextOptions<TContext>`を受け入れるコンストラクターを公開することです。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` は、接続先のデータベースなど、すべての設定をコンテキストに伝えます。 これは、コンテキストで OnConfiguring メソッドを実行して作成されたオブジェクトと同じです。

## <a name="writing-tests"></a>テストの作成

このプロバイダーを使用してテストするための鍵は、InMemory プロバイダーを使用するようにコンテキストに指示し、インメモリデータベースのスコープを制御する機能です。 通常は、各テストメソッドにクリーンなデータベースが必要です。

次に、InMemory データベースを使用するテストクラスの例を示します。 各テストメソッドには一意のデータベース名が指定されています。つまり、それぞれの方法に独自の InMemory データベースがあります。

>[!TIP]
> `.UseInMemoryDatabase()` 拡張メソッドを使用するには、NuGet パッケージの " [Microsoft. EntityFrameworkCore. InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)" を参照します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
