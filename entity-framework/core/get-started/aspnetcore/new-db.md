---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 2eb1668b8c077fabc9cb21088452fd1bead7ff22
ms.sourcegitcommit: ea1cdec0b982b922a59b9d9301d3ed2b94baca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66452238"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a>新しいデータベースを使用した ASP.NET Core での EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。 このチュートリアルでは、移行を使用してデータ モデルからデータベースを作成します。

このチュートリアルは、Windows 上の Visual Studio 2017 または Windows、macOS または Linux. 上の .NET Core CLI で利用できます。

この記事のサンプルは GitHub で確認してください。
* [Visual Studio 2017 と SQL Server](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* [.NET Core CLI と SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).

## <a name="prerequisites"></a>必須コンポーネント

以下のソフトウェアをインストールします。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以下のワークロードで [Visual Studio 2017 バージョン 15.7 以降](https://www.visualstudio.com/downloads/):
  * **ASP.NET と Web 開発** ( **[Web & Cloud]\(Web とクラウド\)** の下)
  * **.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

---

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 2017 を開きます
* **[ファイル] > [新規] > [プロジェクト]**
* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* 名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。
* **[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。
  * ドロップダウン リストで、 **[.NET Core]** と **[ASP.NET Core 2.1]** が選択されていることを確認します。
  * **[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。
  * **[認証]** に **[認証なし]** が設定されていることを確認します。
  * **[OK]** をクリックします。

警告 : **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に Entity Framework Core モデルが追加されます。 このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはこの既存のクラスを拡張するかを選択できます。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* MVC プロジェクトを作成するために、次のコマンドを実行します。

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* プロジェクト ディレクトリに移動します。 入力した次のコマンドは、新しいプロジェクトに対して実行する必要があります。

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。 使用可能なプロバイダーの一覧については、「[Database Providers](../../providers/index.md)」 (データベース プロバイダー) をご覧ください。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。 この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。

* 次のコマンドを実行して、SQLite プロバイダーをインストールします。

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Models** フォルダーを右クリックし、 **[追加] > [クラス]** の順に選択します。
* 名前に「**Model.cs**」を入力して **[OK]** をクリックします。
* このファイルの内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 以下のコードを使用して、**Models** フォルダーで、**Model.cs** を作成します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

実稼働アプリでは、通常別のファイルに各クラスが記述されます。 わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。

## <a name="register-the-context-with-dependency-injection"></a>依存関係挿入にコンテキストを登録する

`BloggingContext` を MVC コント ローラーで使用できるようにするには、`Startup.cs` にサービスとして登録します。

サービス (`BloggingContext` など) はアプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)によって登録され、コンストラクターのパラメーターやプロパティを使用してサービス (MVC コントローラーなど) を使用するコンポーネントに自動的に提供できるようになります。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Startup.cs** に次の `using` ステートメントを追加します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* 次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* **Startup.cs** に次の `using` ステートメントを追加します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* 次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

実際のアプリでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。 わかりやすくするために、このチュートリアルではコード内に定義します。 詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。

## <a name="create-the-database"></a>データベースの作成

次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**
* 次のコマンドを実行します。

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  `The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。

  この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。 `Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 次のコマンドを実行します。

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。 `database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。

---

## <a name="create-a-controller"></a>コントローラーを作成する

`Blog` エンティティのコントローラーとビューをスキャフォールディングします。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、 **[追加] > [コントローラー]** の順に選択します。
* **[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、 **[追加]** をクリックします。
* **[モデル クラス]** に **Blog** を、 **[データ コンテキスト クラス]** に **BloggingContext** を設定します。
* **[追加]** をクリックします。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 次のコマンドを実行します。

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  `tool install` コマンドと `add package` コマンドでは、コントローラーとビューをスキャフォールディングするツールがインストールされます。 `restore` コマンドでは、すべてのプロジェクトのパッケージがダウンロードされることを保証し、`aspnet-codegenerator` コマンドによってスキャフォールディングが実行されます。
---

スキャフォールディング エンジンによって次のファイルが作成されます。

* コントローラー (*Controllers/BlogsController.cs*)
* 作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー (_Views/Blogs/*.cshtml_)

## <a name="run-the-application"></a>アプリケーションの実行

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **[デバッグ]**  >  **[デバッグなしで開始]**

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet run
```
---

* `/Blogs` に移動します。

* **[新規作成]** リンクを使用して、いくつかのブログ エントリを作成します。

  ![[作成] ページ](_static/create.png)

* **[詳細]** 、 **[編集]** および **[削除]** リンクをテストします。

  ![インデックス ページ](_static/index-new-db.png)

## <a name="additional-tutorials"></a>追加のチュートリアル

* [SQLite を使用した新しいデータベースを含む .NET Core での EF Core の概要](xref:core/get-started/netcore/new-db-sqlite)
* ASP.NET Core MVC:
  * [ASP.NET Core MVC の概要](/aspnet/core/tutorials/first-mvc-app/start-mvc)
  * [ASP.NET MVC Web アプリでの EF Core の概要](/aspnet/core/data/ef-mvc/intro)
* [Razor Pages](/aspnet/core/razor-pages/index):
  * [ASP.NET Core の Razor Pages の概要](/aspnet/core/tutorials/razor-pages/razor-pages-start)
  * [ASP.NET Core での Entity Framework Core を使用した Razor Pages](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
