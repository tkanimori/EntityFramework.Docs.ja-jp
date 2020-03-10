---
title: 概要 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 0e7a1ee159cdf5b72448fe6d73c972975b1ab95b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412867"
---
# <a name="getting-started-with-ef-core"></a>EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。

このチュートリアルは、Windows 上の Visual Studio または Windows、macOS または Linux. 上の .NET Core CLI を対象としています。

[この記事のサンプルは GitHub で確認してください](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。

## <a name="prerequisites"></a>必須コンポーネント

以下のソフトウェアをインストールします。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core)。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):
  * **.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)

---

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio を開く
* **[新しいプロジェクトの作成]** をクリックします。
* **[C#]** タグを持つ **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します
* 名前に「**EFGetStarted**」を入力して **[作成]** をクリックします。

---

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。 このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。 使用可能なプロバイダーの一覧については、「[Database Providers](../providers/index.md)」 (データベース プロバイダー) をご覧ください。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**
* 次のコマンドを実行します。

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

ヒント :プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択してパッケージをインストールすることもできます。

---

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 以下のコードを使用して、プロジェクト ディレクトリで **Model.cs** を作成します

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* プロジェクト名を右クリックし、 **[追加]、[クラス]** の順に選択します。
* 名前に「**Model.cs**」を入力して **[追加]** をクリックします。
* このファイルの内容を次のコードに置き換えます。

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core では、既存のデータベースからモデルを[リバース エンジニアリング](../managing-schemas/scaffolding.md)することもできます。

ヒント :実際のアプリでは、クラスはそれぞれ別々のファイルに記述し、[接続文字列](../miscellaneous/connection-strings.md)は構成ファイルまたは環境変数に記述します。 チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。

## <a name="create-the-database"></a>データベースの作成

次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

* 次のコマンドを実行します。

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  これにより、プロジェクトでコマンドを実行するために必要な [dotnet ef](../miscellaneous/cli/dotnet.md) と設計パッケージがインストールされます。 この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。 `database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **パッケージ マネージャー コンソール**で、次のコマンドを実行します。

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  これにより [EF Core 用の PMC ツール](../miscellaneous/cli/powershell.md)がインストールされます。 この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。 `Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。

---

## <a name="create-read-update--delete"></a>作成、読み取り、更新、および削除

* *Program.cs* を開き、内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>アプリを実行する

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio で、.NET Core コンソール アプリの実行時に使用される作業ディレクトリには一貫性がありません。 ([dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619) を参照)。これにより、次の例外がスローされます: "*そのようなテーブルはありません:Blogs*"。 作業ディレクトリを更新するには:

* プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。
* *[TargetFramework]* プロパティの直下に、次を追加します。

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* ファイルを保存する

これで次のように、そのアプリを実行できます。

* **[デバッグ] > [デバッグなしで開始]**

---

## <a name="next-steps"></a>次の手順

* Web アプリでの EF Core の使用には、[ASP.NET Core のチュートリアル](/aspnet/core/data/ef-rp/intro)のページを参照してください
* [LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください
* [required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します
* モデルの変更後のデータベース スキーマの更新に、[Migrations](xref:core/managing-schemas/migrations/index) を使用します
