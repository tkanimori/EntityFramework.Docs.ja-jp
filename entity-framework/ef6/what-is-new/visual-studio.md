---
title: Visual Studio のリリース - EF6
author: divega
ms.date: 2018-07-05
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: aa5409506eeea599421608ddb2dd891df0413961
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997386"
---
# <a name="visual-studio-releases"></a>Visual Studio のリリース

.NET、NuGet、および Entity Framework の最新のツールが含まれているため、常に最新バージョンの Visual Studio を使用するをお勧めします。
実際には、さまざまなサンプルと Entity Framework のドキュメント全体のチュートリアルは、Visual Studio の最新バージョンを使用していることをとします。

ただし、アカウントを使用して、さまざまなバージョンの Entity Framework の以前のバージョンの Visual Studio に限り、いくつかの違いはできます。

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7年以降

- このバージョンの Visual Studio は、Entity Framework のツールと、EF 6.2 ランタイムの最新リリースを含み、追加のセットアップ手順は必要ありません。
参照してください[新](~/ef6/what-is-new/index.md)これらのリリースの詳細についてはします。
- Entity Framework の EF ツールを使用して新しいプロジェクトに追加すると、EF 6.2 NuGet パッケージが自動的に追加します。
手動でインストールまたは使用可能な任意の EF の NuGet パッケージをオンラインでアップグレードすることができます。
- 既定では、このバージョンの Visual Studio で使用可能な SQL Server インスタンスは、MSSQLLocalDB と呼ばれる LocalDB インスタンスです。
必要がありますを使用する接続文字列のサーバーのセクションは、"(localdb)\\MSSQLLocalDB"。
付いて逐語的文字列を使用して、忘れず`@`または二重バック スラッシュ"\\\\"とする C# コードで接続文字列を指定する場合。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 を Visual Studio 2017 15.6

- これらのバージョン Visual Studio にはでは、Entity Framework のツールとランタイム 6.1.3 が含まれます。
参照してください[過去解放](~/ef6/what-is-new/past-releases.md#ef-613)これらのリリースの詳細についてはします。
- Entity Framework を EF ツールを使用して新しいプロジェクトに追加すると、EF 6.1.3 が自動的に追加 NuGet パッケージ。
手動でインストールまたは使用可能な任意の EF の NuGet パッケージをオンラインでアップグレードすることができます。
- 既定では、このバージョンの Visual Studio で使用可能な SQL Server インスタンスは、MSSQLLocalDB と呼ばれる LocalDB インスタンスです。
必要がありますを使用する接続文字列のサーバーのセクションは、"(localdb)\\MSSQLLocalDB"。
付いて逐語的文字列を使用して、忘れず`@`または二重バック スラッシュ"\\\\"とする C# コードで接続文字列を指定する場合。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- このバージョン Visual Studio にはが含まれていて、古いバージョンの Entity Framework のツールとランタイム。
Entity Framework Tools 6.1.3 にアップグレードすることをお勧めを使用して[インストーラー](https://www.microsoft.com/en-us/download/details.aspx?id=40762) Microsoft ダウンロード センターで使用できます。
参照してください[過去解放](~/ef6/what-is-new/past-releases.md#ef-613)これらのリリースの詳細についてはします。
- Entity Framework をアップグレード後の EF ツールを使用して新しいプロジェクトに追加すると、EF 6.1.3 が自動的に追加 NuGet パッケージ。
手動でインストールまたは使用可能な任意の EF の NuGet パッケージをオンラインでアップグレードすることができます。
- 既定では、このバージョンの Visual Studio で使用可能な SQL Server インスタンスは、MSSQLLocalDB と呼ばれる LocalDB インスタンスです。
必要がありますを使用する接続文字列のサーバーのセクションは、"(localdb)\\MSSQLLocalDB"。
付いて逐語的文字列を使用して、忘れず`@`または二重バック スラッシュ"\\\\"とする C# コードで接続文字列を指定する場合。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- このバージョン Visual Studio にはが含まれていて、古いバージョンの Entity Framework のツールとランタイム。
Entity Framework Tools 6.1.3 にアップグレードすることをお勧めを使用して[インストーラー](https://www.microsoft.com/en-us/download/details.aspx?id=40762) Microsoft ダウンロード センターで使用できます。
参照してください[過去解放](~/ef6/what-is-new/past-releases.md#ef-613)これらのリリースの詳細についてはします。
- Entity Framework をアップグレード後の EF ツールを使用して新しいプロジェクトに追加すると、EF 6.1.3 が自動的に追加 NuGet パッケージ。
手動でインストールまたは使用可能な任意の EF の NuGet パッケージをオンラインでアップグレードすることができます。
- 既定では、このバージョンの Visual Studio で使用可能な SQL Server インスタンスは、v11.0 と呼ばれる LocalDB インスタンスです。
必要がありますを使用する接続文字列のサーバーのセクションは、"(localdb)\\v11.0"。
付いて逐語的文字列を使用して、忘れず`@`または二重バック スラッシュ"\\\\"とする C# コードで接続文字列を指定する場合。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- このバージョンの Visual Studio で使用できるエンティティ フレームワーク ツールのバージョンでは、Entity Framework 6 のランタイムと互換性がないと、アップグレードできません。
- 既定では、Entity Framework ツールは、プロジェクトに Entity Framework 4.0 を追加します。
すべての新しいバージョンの EF を使用してアプリケーションを作成するために必要がありますをインストールする、 [NuGet パッケージ マネージャー拡張機能](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)します。
- 既定では、EF ツールのバージョンですべてのコード生成を EntityObject および Entity Framework 4 に基づきます。
DbContext と Entity Framework 5、に基づいて用 DbContext のコード生成テンプレートをインストールすることによって、コード生成を切り替えることをお勧めします。 [c#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)または[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)します。
- NuGet パッケージ マネージャー拡張機能をインストールすると、手動でインストールまたはオンラインで利用可能な任意の EF の NuGet パッケージをアップグレードでき EF6 を使用して、デザイナーが必要としない Code First で使用できます。
- 既定では、このバージョンの Visual Studio で使用可能な SQL Server インスタンスは、SQL Server Express SQLEXPRESS という名前です。
必要がありますを使用する接続文字列のサーバーのセクションは、"。\\SQLEXPRESS"。
付いて逐語的文字列を使用して、忘れず`@`または二重バック スラッシュ"\\\\"とする C# コードで接続文字列を指定する場合。
