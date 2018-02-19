---
title: "ASP.NET Core - 新しいデータベース - EF Core の概要"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: f6ed19d3c5d2ae8d1f5756558e50c1f0dddd2f07
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2018
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a>新しいデータベースを使用した ASP.NET Core での EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。 EF Core モデルからの移行によってデータベースを作成します。 Entity Framework Core のチュートリアルについては「[その他のリソース](#additional-resources)」をご覧ください。

このチュートリアルでは、次のものが必要になります。
* [Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) および以下のワークロード。
  * **ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)
  * **.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)は GitHub で確認できます。

## <a name="create-a-new-project-in-visual-studio-2017"></a>Visual Studio 2017 で新しいプロジェクトを作成する

* **[ファイル] > [新規] > [プロジェクト]**
* 左側のメニューから **[インストール済み] > [テンプレート] > [Visual C#] > [.NET Core]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* 名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。
* **[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。
  * ドロップダウン リストで、**[.NET Core]** と **[ASP.NET Core 2.0]** のオプションが選択されていることを確認します。
  * **[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。
  * **[認証]** に **[認証なし]** が設定されていることを確認します。
  * **[OK]** をクリックします。

注意: **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に EntityFramework Core モデルが追加されます。 このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはエンティティ クラスを拡張するかを選択できます。

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

対象となる EF Core データベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

いくつかの Entity Framework Core Tools を使用して、EF Core モデルからデータベースを作成します。 そのため、ツールのパッケージをインストールします。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

後で、いくつかの ASP.NET Core スキャフォールディング ツールを使用して、コント ローラーとビューを作成します。 そのため、このデザイン パッケージもインストールします。

* `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design` を実行します。

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキストおよびエンティティ クラスを定義します。

* **Models** フォルダーを右クリックし、**[追加] > [クラス]** の順に選択します。
* 名前に「**Model.cs**」を入力して **[OK]** をクリックします。
* このファイルの内容を次のコードに置き換えます。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

注: 実際のアプリでは、モデルの各クラスは別のファイルに記述するのが一般的です。 わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。

## <a name="register-your-context-with-dependency-injection"></a>依存関係の挿入にコンテキストを登録します。

サービス (`BloggingContext` など) は、アプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に登録されます。 これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。

MVC コントローラーで利用するために、`BloggingContext` をサービスとして登録します。

* **Startup.cs** を開きます。
* 次の `using` ステートメントを追加します。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

`AddDbContext` メソッドを追加して、それをサービスとして登録します。

* `ConfigureServices` メソッドに次のコードを追加します。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

注: 実際のアプリでは、接続文字列は構成ファイルに記述するのが一般的です。 わかりやすくするために、接続文字列はコード内に定義しています。 詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。

## <a name="create-your-database"></a>データベースを作成する

モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成できます。

* PMC を開きます。

  **[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**
* `Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。 `The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。
* `Update-Database` を実行して、新しい移行をデータベースに適用します。 このコマンドは、移行を適用する前に、データベースを作成します。

## <a name="create-a-controller"></a>コントローラーを作成する

プロジェクトでスキャフォールディングを有効にします。

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。
* **[最小の依存関係]** を選択し、**[追加]** をクリックします。
* *ScaffoldingReadMe.txt* ファイルは無視しても、削除してもかまいません。

スキャフォールディングが有効になったら、`Blog` エンティティのコントローラーをスキャフォールディングできます。

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。
* **[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[OK]** をクリックします。
* **[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **BloggingContext** を設定します。
* **[追加]**をクリックします。


## <a name="run-the-application"></a>アプリケーションの実行

F5 キーを押してアプリを実行し、テストします。

* `/Blogs` に移動します。
* [作成] リンクを使用して、いくつかのブログ エントリを作成します。 [詳細] リンクと [削除] リンクをテストします。

![イメージ](_static/create.png)

![イメージ](_static/index-new-db.png)

## <a name="additional-resources"></a>その他のリソース

* [EF - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。
* [Mac または Linux での ASP.NET Core MVC の概要](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [Visual Studio を使用した ASP.NET Core MVC の概要](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
