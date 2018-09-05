---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.date: 08/08/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: c243ef2a1940af9bf4f4b32f17acfcce7f972862
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996911"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a>データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要

このチュートリアルでは、Entity Framework Core を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。

## <a name="prerequisites"></a>必須コンポーネント

* [Windows 10 Fall Creators Update (10.0; Build 16299) 以降](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).

* **ユニバーサル Windows プラットフォーム開発**ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.7 以降。

* [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/core)。

## <a name="create-a-model-project"></a>モデル プロジェクトを作成する

> [!IMPORTANT]
> .NET Core ツールが UWP プロジェクトとやりとりする方法に制限があるため、モデルを UWP 以外のプロジェクトに配置し、**パッケージ マネージャー コンソール** (PMC) で移行コマンドを実行できるようにする必要があります。

* Visual Studio を開く

* **[ファイル] > [新規] > [プロジェクト]**

* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Standard]** の順に選択します。

* **[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** テンプレートを選択します。

* プロジェクト *Blogging.Model* に名前を付けます。

* ソリューション *Blogging* に名前を付けます。

* **[OK]** をクリックします。

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは、SQLite を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**。

* `Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。

このチュートリアルの後半では、複数の Entity Framework Core ツールを使用してデータベースのメンテナンスを行います。 そのため、そのツールのパッケージもインストールしてください。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

## <a name="create-the-model"></a>モデルを作成する

ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。

* *Class1.cs* を削除します。

* 次のコードを使用して、*Model.cs* を作成します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a>新しい UWP プロジェクトを作成する

* **ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。

* 左側のメニューから **[インストール済み] > [Visual C#] > [Windows ユニバーサル]** の順に選択します。

* **[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。

* プロジェクト *Blogging.UWP* に名前を付け、**[OK]** をクリックします。

* ターゲットおよび最小バージョンを、**Windows 10 Fall Creators Update (10.0; build 16299.0)** 以上に設定します。

## <a name="create-the-initial-migration"></a>初期移行を作成する

これでモデルの用意ができましたので、初めて実行されたときにデータベースが作成されるように、アプリを設定します。 このセクションでは、初期移行を作成します。 次のセクションでは、アプリの起動時にこの移行を適用するコードを追加します。

移行ツールでは UWP 以外のスタートアップ プロジェクトが必要であるため、まずそれを作成します。

* **ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。

* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。

* **[コンソール アプリ (.NET Core)]** プロジェクト テンプレートを選択します。

* プロジェクト *Blogging.Migrations.Startup* に名前を付けて、**[OK]** をクリックします。

* *Blogging.Migrations.Startup* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。

これで、ご自分の初期移行を作成できるようになりました。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* **[既定のプロジェクト]** として *Blogging.Model* プロジェクトを選択します。

* **ソリューション エクスプローラー**で、*Blogging.Migrations.Startup* プロジェクトをスタートアップ プロジェクトに設定します。

* `Add-Migration InitialCreate` を実行します。

  このコマンドでは、ご利用のモデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。

## <a name="create-the-database-on-app-startup"></a>アプリの起動時にデータベースを作成する

アプリが実行されているデバイス上にデータベースが作成されるようにしたいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。 これにより、アプリの初回実行時にローカル データベースの作成が行われます。

* *Blogging.UWP* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。

* *App.xaml.cs* を開きます。

* 保留中の移行を適用する強調表示されたコードを追加します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> ご利用のモデルを変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングして、対応する変更をデータベースに適用できます。 保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。
>
>EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。

## <a name="use-the-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *MainPage.xaml* を開きます。

* 以下の強調表示されたページ読み込みハンドラーと UI コンテンツを追加します。

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

ここで、UI とデータベースを接続するためのコードを追加する

* *MainPage.xaml.cs* を開きます。

* 次の一覧の強調表示されたコードを追加します。

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

アプリケーションを実行して動作を確認できるようになりました。

* **ソリューション エクスプローラー**で、*Blogging.UWP* プロジェクトを右クリックし、次に **[配置]** を選択します。

* スタートアップ プロジェクトとして、*Blogging.UWP* を設定します。

* **[デバッグ] > [デバッグなしで開始]**

  テストがビルドされ、実行されます。

* URL を入力して **[追加]** をクリックします。

  ![イメージ](_static/create.png)

  ![イメージ](_static/list.png)

  さあ、できました! これで Entity Framework Core を実行するシンプルな UWP アプリが完成しました。

## <a name="next-steps"></a>次の手順

EF Core と UWP を一緒に使用する場合に把握しておく必要がある互換性とパフォーマンスに関する情報については、「[EF Core でサポートされている .NET 実装](../../platforms/index.md#universal-windows-platform)」を参照してください。

Entity Framework Core の機能の詳細については、このドキュメントの他の記事をご覧ください。
