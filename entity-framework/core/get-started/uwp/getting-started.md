---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.topic: get-started-article
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
ms.technology: entity-framework-core
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: f743ff5392d1f30283a13d2e7fb8029be88387aa
ms.sourcegitcommit: 96324e58c02b97277395ed43173bf13ac80d2012
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
ms.locfileid: "26049835"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a>データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要

> [!NOTE]
> このチュートリアルでは、EF Core 2.0.1 (ASP.NET Core と .NET Core SDK 2.0.3 と共にリリース) を使用します。 EF Core 2.0.0 では、良好な UWP エクスペリエンスに必要な、重要なバグ修正が一部不足しています。

このチュートリアルでは、Entity Framework を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。

> [!IMPORTANT]
> **UWP 上の LINQ クエリでは、匿名型を使用しないことを考慮してください**。 UWP アプリケーションをアプリ ストアに配置するには、アプリケーションを .NET ネイティブでコンパイルする必要があります。 匿名型のクエリは、.NET ネイティブではパフォーマンスが低下します。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite)は GitHub で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを実施するのに必要な項目は次のとおりです。

* [Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)

* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 以降。

* **Universal Windows Platform Development** ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.4 以降。

## <a name="create-a-new-model-project"></a>新しいモデル プロジェクトを作成する

> [!WARNING]
> .NET Core ツールが UWP プロジェクトとやりとりする方法に制限があるため、モデルを UWP 以外のプロジェクトに配置し、パッケージ マネージャー コンソールで移行コマンドを実行できるようにする必要があります。

* Visual Studio を開く

* [ファイル] > [新規] > [プロジェクト...]

* 左側のメニューから [テンプレート] > [Visual C#] を選択します。

* **[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** プロジェクト テンプレートを選択します。

* プロジェクト名を設定し、**[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQLite を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]

* `Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。

このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。 そこで、そのツールのパッケージもインストールします。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

* .csproj ファイルを編集して `<TargetFramework>netstandard2.0</TargetFramework>` を `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>` に置き換えます。

## <a name="create-your-model"></a>モデルを作成する

ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。

* [プロジェクト] > [クラスの追加...]

* 名前に「*Model.cs*」を入力して **[OK]** をクリックします。

* このファイルの内容を次のコードに置き換えます。

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a>新しい UWP プロジェクトを作成する

* Visual Studio を開く

* [ファイル] > [新規] > [プロジェクト...]

* 左側のメニューから、[テンプレート] > [Visual C#] > [Windows ユニバーサル] を選択します。

* **[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。

* プロジェクト名を設定し、**[OK]** をクリックします。

* ターゲット バージョンと最小バージョンを `Windows 10 Fall Creators Update (10.0; build 16299.0)` 以上に設定します。

## <a name="create-your-database"></a>データベースを作成する

これでモデルができたので、移行を利用して自分用のデータベースを作成できます。

* [ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]

* モデル プロジェクトを既定のプロジェクトに選択し、スタートアップ プロジェクトに設定します。

* `Add-Migration MyFirstMigration` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。

アプリが実行されているデバイス上にデータベースを作成したいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。 これにより、アプリの初回実行時にローカル データベースの作成が自動的に管理されることになります。

* **ソリューション エクスプローラー**で **[App.xaml]** を右クリックし、**[コードの表示]** を選択します。

* 強調表示された using をファイルの先頭に追加します。

* 保留中の移行を適用する強調表示されたコードを追加します。

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/App.xaml.cs?highlight=1,25-28)]

> [!TIP]  
> モデルを今後変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応する変更をデータベースに適用できます。 保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。
>
>EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。

## <a name="use-your-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *MainPage.xaml* を開きます。

* 以下の強調表示されたページ読み込みハンドラーと UI コンテンツを追加します。

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml?highlight=9,11-23)]

ここで、UI とデータベースを接続するためのコードを追加します。

* **ソリューション エクスプローラー**で **[MainPage.xaml]** を右クリックし、**[コードの表示]** を選択します。

* 次の一覧の強調表示されたコードを追加します。

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml.cs?highlight=30-48)]

アプリケーションを実行して動作を確認できるようになりました。

* [デバッグ] > [デバッグなしで開始]

* アプリケーションがビルドされ、起動されます。

* URL を入力して **[追加]** をクリックします。

![image](_static/create.png)

![image](_static/list.png)

## <a name="next-steps"></a>次のステップ

> [!TIP]
> エンティティ型に [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx)、[`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx)、[`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) を実装し、`ChangeTrackingStrategy.ChangingAndChangedNotifications` を使用することで、`SaveChanges()` のパフォーマンスを向上させることができます。

さあ、できました! Entity Framework を実行する簡単な UWP アプリが完成しました。

Entity Framework の機能の詳細については、このドキュメントの他の記事をご覧ください。
