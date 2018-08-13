---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 08/03/2018
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 9e86bc9cff028ad9791f23cbb45f0a93110c0064
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614351"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a>新しいデータベースを使用した ASP.NET Core での EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。 EF Core モデルからの移行によってデータベースを作成します。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)。

## <a name="prerequisites"></a>必須コンポーネント

以下のソフトウェアをインストールします。

* [Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) および以下のワークロード。
  * **ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)
  * **.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

## <a name="create-a-new-project-in-visual-studio-2017"></a>Visual Studio 2017 で新しいプロジェクトを作成する

* Visual Studio 2017 を開きます
* **[ファイル] > [新規] > [プロジェクト]**
* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* 名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。
* **[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。
  * ドロップダウン リストで、**[.NET Core]** と **[ASP.NET Core 2.1]** のオプションが選択されていることを確認します。
  * **[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。
  * **[認証]** に **[認証なし]** が設定されていることを確認します。
  * **[OK]** をクリックします。

注意: **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に EntityFramework Core モデルが追加されます。 このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはこの既存のクラスを拡張するかを選択できます。

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。 

このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。 この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。

* **Models** フォルダーを右クリックし、**[追加] > [クラス]** の順に選択します。
* 名前に「**Model.cs**」を入力して **[OK]** をクリックします。
* このファイルの内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

実際のアプリでは、モデルの各クラスは別のファイルに記述するのが一般的です。 わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。

## <a name="register-your-context-with-dependency-injection"></a>依存関係の挿入にコンテキストを登録します。

サービス (`BloggingContext` など) は、アプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に登録されます。 これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。

`BloggingContext` を MVC コント ローラーで使用できるようにするには、サービスとして登録します。

* **Startup.cs** を開きます。
* 次の `using` ステートメントを追加します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

`AddDbContext` メソッドを呼び出して、コンテキストをサービスとして登録します。

* 次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=13-14)]

注: 実際のアプリでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。 わかりやすくするために、このチュートリアルではコード内に定義します。 詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。

## <a name="create-the-database"></a>データベースの作成

モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成できます。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**
* `Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。 `The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。
* `Update-Database` を実行して、新しい移行をデータベースに適用します。 このコマンドは、移行を適用する前に、データベースを作成します。

## <a name="create-a-controller"></a>コントローラーを作成する

`Blog` エンティティのコントローラーとビューをスキャフォールディングします。

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。
* **[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[追加]** をクリックします。
* **[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **BloggingContext** を設定します。
* **[追加]** をクリックします。


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
