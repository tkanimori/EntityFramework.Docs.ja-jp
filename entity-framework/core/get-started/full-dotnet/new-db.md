---
title: .NET Framework - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 08/06/2018
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: 088ac915041489242eb8090e7bf3a2bdc8036534
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614429"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a>新しいデータベースを使用した .NET Framework での EF Core の概要

このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。 モデルからの移行によってデータベースを作成します。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)。

## <a name="prerequisites"></a>必須コンポーネント

* [Visual Studio 2017 バージョン 15.7 以降](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio 2017 を開きます

* **[ファイル] > [新規] > [プロジェクト]**

* 左側のメニューから **[インストール済み] > [Visual C#] > [Windows デスクトップ]** の順に選択します。

* **[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。

* プロジェクトの対象が **.NET Framework 4.6.1** 以降であることを確認します。

* プロジェクトに *ConsoleApp.NewDb* という名前を付けて **[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは、SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。 そのため、そのツールのパッケージもインストールしてください。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

## <a name="create-the-model"></a>モデルを作成する

ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。

* **[プロジェクト] > [クラスの追加]**

* 名前に「*Model.cs*」を入力して **[OK]** をクリックします。

* このファイルの内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] 

> [!TIP]  
> 実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は構成ファイルまたは環境変数にそれぞれ記述します。 わかりやすくするために、このチュートリアルではすべてを 1 つのコード ファイルに記述しています。

## <a name="create-the-database"></a>データベースの作成

これでモデルができたので、移行を利用してデータベースを作成できます。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* `Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。

* `Update-Database` を実行して、新しい移行をデータベースに適用します。 データベースがまだ存在しないため、データベースが作成されてから移行が適用されます。

> [!TIP]  
> モデルを変更する場合、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応するスキーマの変更をデータベースに対して行います。 スキャフォールディング コードを確認 (および必要な変更) したら、`Update-Database` コマンドを使用してデータベースに変更を適用できます。
>
> EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。

## <a name="use-the-model"></a>モデルを使用する

モデルを使用してデータ アクセスを実行できるようになりました。

* *Program.cs* を開きます。

* このファイルの内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)]

* **[デバッグ] > [デバッグなしで開始]**

  1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。

  ![イメージ](_static/output-new-db.png)

## <a name="additional-resources"></a>その他のリソース

* [既存のデータベースを使用した .NET Framework での EF Core](xref:core/get-started/full-dotnet/existing-db)
* [新しいデータベース SQLite を使用した .NET core での EF Core](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。
