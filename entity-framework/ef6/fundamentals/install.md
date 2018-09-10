---
title: Entity Framework の EF6 を取得します。
author: divega
ms.date: 2016-10-23
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 601f8d123d5494be6a658da1c4ad3743ed50385c
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250882"
---
# <a name="get-entity-framework"></a>Entity Framework を入手します。
Entity Framework は、Visual Studio と EF のランタイムの EF ツールの構成されます。

## <a name="ef-tools-for-visual-studio"></a>EF の Tools for Visual Studio

Entity Framework Tools for Visual Studio は EF Designer、EF モデル ウィザードは、最初に、データベースに必要なし、最初のワークフロー モデルします。 すべての最新バージョンの Visual Studio では、EF ツールが含まれます。 項目を確認する必要がありますが、Visual Studio のカスタム インストールを実行する場合、それを含むワークロードを選択するか、または個々 のコンポーネントとして選択して、"Entity Framework 6 Tools"が選択されます。

一部が Visual Studio の以前のバージョンには、更新された EF ツールは、ダウンロードとして入手できます。 参照してください[バージョンの Visual Studio](~/ef6/what-is-new/visual-studio.md) Visual Studio のバージョンの EF ツールの最新バージョンを取得する方法のガイダンスについて。

## <a name="ef-runtime"></a>EF ランタイム

最新バージョンの Entity Framework は、 [EntityFramework NuGet パッケージ](http://nuget.org/packages/EntityFramework/)します。 使い慣れた NuGet パッケージ マネージャーを使用しない場合は、ぜひを読み取る、 [NuGet の概要](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)します。

### <a name="installing-the-ef-nuget-package"></a>EF の NuGet パッケージをインストールします。

右クリックし、EntityFramework パッケージをインストールすることができます、**参照**、プロジェクトのフォルダーを選択して**NuGet パッケージの管理.**

![NuGet パッケージを管理します。](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>パッケージ マネージャー コンソールからインストールします。

または、EntityFramework をインストールで、次のコマンドを実行して、[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)します。

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>EF の特定のバージョンをインストールします。

新しいバージョンの EF ランタイムは EF 4.1 以降からとしてリリースされていますが、 [EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)します。 これらのバージョンの Visual studio の次のコマンドを実行して .NET Framework ベースのプロジェクトに追加できます[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

``` powershell
Install-Package EntityFramework -Version <number>
```

なお`<number>`をインストールする EF の特定のバージョンを表します。 たとえば、6.2.0、EF 6.2 の番号のバージョンです。   

4.1 の前に EF のランタイムでは、.NET Framework の一部であったし、個別にインストールすることはできません。

### <a name="installing-the-latest-preview"></a>最新のプレビューをインストールします。

上記のメソッドが、最新バージョンを与える Entity Framework のリリースを完全にサポートします。 プレリリース バージョンの Entity Framework をぜひお試しし、についてフィードバックを提供する利用可能なことは少なくありません。

選択 EntityFramework の最新のプレビューをインストールする**プレリリース版を含む**NuGet パッケージの管理 ウィンドウでします。 プレリリース バージョンが利用できない場合は、最新を自動的に取得が完全にサポートされているバージョンの Entity Framework。

![プレリリースを含める](~/ef6/media/includeprerelease.png)

また、次のコマンドを実行することができます、[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)します。

``` powershell
Install-Package EntityFramework -Pre
```
