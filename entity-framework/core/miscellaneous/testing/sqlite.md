---
title: SQLite を使用したテスト-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: f7f847d8c766c0d4d7577ea6760ee72a17f84933
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414633"
---
# <a name="testing-with-sqlite"></a>SQLite のテスト

SQLite にはメモリ内モードがあり、これを使用すると、実際のデータベース操作のオーバーヘッドを発生させることなく、リレーショナルデータベースに対して SQLite を使用してテストを作成することができます。

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub でご覧いただけます。

## <a name="example-testing-scenario"></a>テストシナリオの例

アプリケーションコードがブログに関連するいくつかの操作を実行できるようにするには、次のサービスを検討してください。 内部的には、SQL Server データベースに接続する `DbContext` を使用します。 このコンテキストをスワップしてメモリ内の SQLite データベースに接続すると、コードを変更しなくても効率的なテストを作成できるようになります。また、コンテキストのテスト double を作成するために作業を行うこともできます。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a>コンテキストの準備

### <a name="avoid-configuring-two-database-providers"></a>2つのデータベースプロバイダーの構成を回避する

テストでは、InMemory プロバイダーを使用するようにコンテキストを外部で構成しようとしています。 コンテキストで `OnConfiguring` をオーバーライドしてデータベースプロバイダーを構成する場合は、データベースプロバイダーがまだ構成されていない場合にのみ構成するように、いくつかの条件付きコードを追加する必要があります。

> [!TIP]  
> ASP.NET Core を使用している場合は、このコードは必要ありません。これは、データベースプロバイダーがコンテキスト (Startup.cs) の外部で構成されているためです。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a>テスト用のコンストラクターを追加する

別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを変更して、`DbContextOptions<TContext>`を受け入れるコンストラクターを公開することです。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> `DbContextOptions<TContext>` は、接続先のデータベースなど、すべての設定をコンテキストに伝えます。 これは、コンテキストで OnConfiguring メソッドを実行して作成されたオブジェクトと同じです。

## <a name="writing-tests"></a>テストの作成

このプロバイダーを使用してテストするための鍵は、SQLite を使用するようにコンテキストに指示し、インメモリデータベースのスコープを制御する機能です。 データベースのスコープは、接続を開いたり閉じたりすることによって制御されます。 データベースのスコープは、接続が開いている期間です。 通常は、各テストメソッドにクリーンなデータベースが必要です。

>[!TIP]
> `SqliteConnection()` と `.UseSqlite()` 拡張メソッドを使用するには、 [NuGet パッケージを](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)参照してください。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
