---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.date: 10/13/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: 456967a0dc981053919064a19cc9c98bf7309865
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022377"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a>データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要

このチュートリアルでは、Entity Framework Core を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。

## <a name="prerequisites"></a>必須コンポーネント

* [Windows 10 Fall Creators Update (10.0; Build 16299) 以降](https://support.microsoft.com/help/4027667/windows-update-windows-10).

* **ユニバーサル Windows プラットフォーム開発**ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.7 以降。

* [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/core)。

> [!IMPORTANT]
> このチュートリアルでは Entity Framework Core[ 移行](xref:core/managing-schemas/migrations/index)コマンドを使用して、データベースのスキーマを作成および更新します。
> これらのコマンドによって、UWP プロジェクトは直接操作されません。
> このため、アプリケーションのデータ モデルは共有ライブラリ プロジェクト内に配置され、コマンドは別の .NET Core コンソール アプリケーションを使用して実行されます。

## <a name="create-a-library-project-to-hold-the-data-model"></a>データ モデルを保持するためのライブラリ プロジェクトを作成する

* Visual Studio を開く

* **[ファイル] > [新規] > [プロジェクト]**

* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Standard]** の順に選択します。

* **[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** テンプレートを選択します。

* プロジェクト *Blogging.Model* に名前を付けます。

* ソリューション *Blogging* に名前を付けます。

* **[OK]** をクリックします。

## <a name="install-entity-framework-core-runtime-in-the-data-model-project"></a>データ モデル プロジェクトに Entity Framework Core ランタイムをインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは、SQLite を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**。

* パッケージ マネージャー コンソール内でライブラリ プロジェクト *Blogging.Model* が **[既定のプロジェクト]** として選択されていることを確認します。

* `Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。

## <a name="create-the-data-model"></a>データ モデルを作成する

ここで、モデルを編成する*DbContext* とエンティティ クラスを定義します。

* *Class1.cs* を削除します。

* 次のコードを使用して、*Model.cs* を作成します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-console-project-to-run-migrations-commands"></a>移行コマンドを実行するための新しいコンソール プロジェクトを作成する

* **ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。

* 左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。

* **[コンソール アプリ (.NET Core)]** プロジェクト テンプレートを選択します。

* プロジェクト *Blogging.Migrations.Startup* に名前を付けて、**[OK]** をクリックします。

* *Blogging.Migrations.Startup* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。

## <a name="install-entity-framework-core-tools-in-the-migrations-startup-project"></a>移行のスタートアップ プロジェクトに Entity Framework Core ツールをインストールする

パッケージ マネージャー コンソール内で EF Core 移行コマンドを有効にするには、EF Core ツールのパッケージをコンソール アプリケーションにインストールします。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* `Install-Package Microsoft.EntityFrameworkCore.Tools -ProjectName Blogging.Migrations.Startup` を実行します。

## <a name="create-the-initial-migration"></a>初期移行を作成する

 最初の移行を作成し、コンソール アプリケーションをスタートアップ プロジェクトとして指定します。

* `Add-Migration InitialCreate -StartupProject Blogging.Migrations.Startup` を実行します。

このコマンドでは、ご利用のデータ モデル用に最初のデータベース テーブル セットを作成する移行がスキャフォールディングされます。

## <a name="create-the-uwp-project"></a>UWP プロジェクトを作成する

* **ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。

* 左側のメニューから **[インストール済み] > [Visual C#] > [Windows ユニバーサル]** の順に選択します。

* **[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。

* プロジェクト *Blogging.UWP* に名前を付け、**[OK]** をクリックします。

> [!IMPORTANT]
> ターゲットおよび最小バージョンを、**Windows 10 Fall Creators Update (10.0; build 16299.0)** 以上に設定します。
> Windows 10 の以前のバージョンでは、Entity Framework Core によって必要とされる .NET Standard 2.0 がサポートされていません。

## <a name="add-code-to-create-the-database-on-application-startup"></a>アプリケーションの起動時にデータベースを作成するためのコードを追加する

アプリが実行されているデバイス上にデータベースが作成されるようにしたいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。 これにより、アプリの初回実行時にローカル データベースの作成が行われます。

* *Blogging.UWP* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。

* *App.xaml.cs* を開きます。

* 保留中の移行を適用する強調表示されたコードを追加します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> ご利用のモデルを変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングして、対応する変更をデータベースに適用できます。 保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。
>
>EF Core はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。

## <a name="use-the-data-model"></a>データ モデルを使用する

EF Core 使用してデータ アクセスを使用できるようになりました。

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
