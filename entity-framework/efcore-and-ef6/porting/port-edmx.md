---
title: EF6 から EF Core に移植して、EDMX ベースのモデルを移植する
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: a1c978e141f47a39fff59eb5fc417a63afd37b04
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71148998"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>EF6 EDMX ベースのモデルを EF Core に移植する

EF Core は、モデルの EDMX ファイル形式をサポートしていません。 これらのモデルを移植する最良の方法は、アプリケーションのデータベースから新しいコードベースのモデルを生成することです。

## <a name="install-ef-core-nuget-packages"></a>NuGet パッケージのインストール EF Core

`Microsoft.EntityFrameworkCore.Tools` NuGet パッケージをインストールします。

## <a name="regenerate-the-model"></a>モデルの再生成

リバースエンジニアリング機能を使用して、既存のデータベースに基づいてモデルを作成できるようになりました。

パッケージマネージャーコンソールで次のコマンドを実行します ([ツール] – > NuGet パッケージマネージャー– > パッケージマネージャーコンソール)。 テーブルのサブセットをスキャフォールディングするためのコマンドオプションについては、「[パッケージマネージャーコンソール (Visual Studio)](../../core/miscellaneous/cli/powershell.md) 」を参照してください。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

たとえば、SQL Server LocalDB インスタンスのブログデータベースからモデルをスキャフォールディングするコマンドを次に示します。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>EF6 モデルの削除

ここで、アプリケーションから EF6 モデルを削除します。

EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションでサイドバイサイドで使用できるようにするためです。 ただし、アプリケーションの任意の領域で EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコードの一部でコンパイルエラーが発生することがあります。

## <a name="update-your-code"></a>コードを更新する

この時点で、コンパイルエラーに対処し、コードを確認して、EF6 と EF Core 間で動作が変化するかどうかを確認します。

## <a name="test-the-port"></a>ポートをテストする

アプリケーションがコンパイルされるので、は EF Core に正常に移植されるという意味ではありません。 アプリケーションのすべての領域をテストして、動作の変更がアプリケーションに悪影響を与えないことを確認する必要があります。
