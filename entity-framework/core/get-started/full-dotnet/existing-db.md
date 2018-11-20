---
title: .NET Framework - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 1b90c491a3b2025da750a3266ff45d9d92bb1d0d
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688616"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a>既存のデータベースを使用した .NET Framework での EF Core の概要

このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。 既存のデータベースのリバース エンジニアリングによって Entity Framework モデルを作成します。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)。

## <a name="prerequisites"></a>必須コンポーネント

* [Visual Studio 2017 バージョン 15.7 以降](https://www.visualstudio.com/downloads/)

## <a name="create-blogging-database"></a>Blogging データベースを作成する

このチュートリアルでは、LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。 **Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略してください。

* Visual Studio を開く

* **[ツール] > [データベースへの接続]**

* **[Microsoft SQL Server]** を選択して **[続行]** をクリックします。

* **[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。

* **[データベース名]** に「**master**」を入力して **[OK]** をクリックします。

* **[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。

* **[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。

* 以下に表示されているスクリプトを、クエリ エディターにコピーします。

* クエリ エディターを右クリックし、**[実行]** を選択します。

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio 2017 を開きます

* **[ファイル] > [新規] > [プロジェクト]**

* 左側のメニューから **[インストール済み] > [Visual C#] > [Windows デスクトップ]** の順に選択します。

* **[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。

* プロジェクトの対象が **.NET Framework 4.6.1** 以降であることを確認します。

* プロジェクトに *ConsoleApp.ExistingDb* という名前を付けて **[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは、SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

次の手順では、いくつかの Entity Framework ツールを使用して、データベースをリバース エンジニアリングします。 そのため、そのツールのパッケージもインストールしてください。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

## <a name="reverse-engineer-the-model"></a>モデルのリバース エンジニアリングを行う

ここで、既存のデータベースに基づいて、EF モデルを作成します。

* **[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**

* 既存のデータベースからモデルを作成するには、次のコマンドを実行します。

  ``` powershell
  Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
  ```

> [!TIP]  
> 上記のコマンドに引数 `-Tables` を追加すると、エンティティを生成するためのテーブルを指定できます。 たとえば、`-Tables Blog,Post` のようにします。

リバース エンジニアリングのプロセスにより、既存のデータベースのスキーマに基づいてエンティティ クラス (`Blog` および `Post`) と派生コンテキスト (`BloggingContext`) が作成されます。

エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。 こちらが、`Blog` と `Post` のエンティティ クラスです。

 [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Post.cs)]

> [!TIP]  
> 遅延読み込みを有効にするため、ナビゲーション プロパティ `virtual` を作成することができます (Blog.Post および Post.Blog)。

このコンテキストはデータベースとのセッションを表します。 エンティティ クラスのインスタンスのクエリと保存に使用できるメソッドが含まれています。

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)]

## <a name="use-the-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *Program.cs* を開きます。

* このファイルの内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] 

* [デバッグ] > [デバッグなしで開始]

  1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。

  ![イメージ](_static/output-existing-db.png)

## <a name="next-steps"></a>次の手順

コンテキスト クラスとエンティティ クラスをスキャフォールディングする方法の詳細については、次の記事を参照してください。
* [リバース エンジニアリング](xref:core/managing-schemas/scaffolding)
* [Entity Framework Core ツールのリファレンス - .NET CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [Entity Framework Core ツールのリファレンス - パッケージ マネージャー コンソール](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)