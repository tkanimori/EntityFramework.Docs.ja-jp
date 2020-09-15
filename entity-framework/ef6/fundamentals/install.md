---
title: Entity Framework の取得-EF6
description: Entity Framework 6 を取得する
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/install
ms.openlocfilehash: 9254f835f5007a213c49557165f6830c30711f1f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072786"
---
# <a name="get-entity-framework"></a>Entity Framework を取得する
Entity Framework は、EF Tools for Visual Studio と EF Runtime で構成されています。

## <a name="ef-tools-for-visual-studio"></a>EF Tools for Visual Studio

Visual Studio の Entity Framework Tools には、EF デザイナーと EF モデルウィザードが含まれており、データベースの最初のワークフローとモデルの最初のワークフローに必要です。 EF ツールは、すべての最新バージョンの Visual Studio に含まれています。 Visual Studio のカスタムインストールを実行する場合は、項目を含むワークロードを選択するか、個々のコンポーネントとして選択することで、項目 "Entity Framework 6 ツール" が選択されていることを確認する必要があります。

以前のバージョンの Visual Studio では、更新された EF ツールはダウンロードとして入手できます。 お使いのバージョンの Visual Studio で使用できる EF ツールの最新バージョンを入手する方法については、「 [Visual studio のバージョン](xref:ef6/what-is-new/visual-studio) 」を参照してください。

## <a name="ef-runtime"></a>EF ランタイム

Entity Framework の最新バージョンは、 [Entityframework NuGet パッケージ](https://nuget.org/packages/EntityFramework/)として入手できます。 NuGet パッケージマネージャーに慣れていない場合は、「 [nuget の概要」](/nuget/consume-packages/overview-and-workflow)をお読みになることをお勧めします。

### <a name="installing-the-ef-nuget-package"></a>EF NuGet パッケージのインストール

EntityFramework パッケージをインストールするには、プロジェクトの [**参照**] フォルダーを右クリックし、[ **NuGet パッケージの管理...** ] を選択します。

![NuGet パッケージの管理](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>パッケージマネージャーコンソールからのインストール

または、 [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、entityframework をインストールすることもできます。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>特定のバージョンの EF のインストール

EF 4.1 以降では、新しいバージョンの EF runtime が [Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)としてリリースされました。 これらのバージョンはいずれも、Visual Studio の [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、.NET Framework ベースのプロジェクトに追加できます。

``` powershell
Install-Package EntityFramework -Version <number>
```

は `<number>` 、インストールする EF の特定のバージョンを表すことに注意してください。 たとえば、6.2.0 は EF 6.2 の number のバージョンです。   

4.1 より前の EF runtime は .NET Framework に含まれており、個別にインストールすることはできません。

### <a name="installing-the-latest-preview"></a>最新のプレビューのインストール

上記の方法では、Entity Framework の完全にサポートされた最新のリリースが提供されます。 多くの場合、プレリリース版の Entity Framework 利用可能であり、お客様にご意見をお寄せください。

EntityFramework の最新のプレビューをインストールするには、[NuGet パッケージの管理] ウィンドウで [ **プレリリースを含める** ] を選択します。 使用可能なプレリリースバージョンがない場合は、最新の完全サポートバージョンの Entity Framework が自動的に取得されます。

![プレリリースを含める](~/ef6/media/includeprerelease.png)

または、 [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行します。

``` powershell
Install-Package EntityFramework -Pre
```
