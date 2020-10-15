---
title: Visual Studio のリリース-EF6
description: Visual Studio リリースおよび Entity Framework 6
author: ajcvickers
ms.date: 07/05/2018
uid: ef6/what-is-new/visual-studio
ms.openlocfilehash: ba6d8255f1cb5393b2f0e3e404f4fd2d1f1621c4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064238"
---
# <a name="visual-studio-releases"></a>Visual Studio のリリース

最新バージョンの Visual Studio を常に使用することをお勧めします。これは、.NET、NuGet、および Entity Framework 用の最新のツールが含まれているためです。
実際、Entity Framework ドキュメントに含まれるさまざまなサンプルとチュートリアルでは、Visual Studio の最新バージョンを使用していることを前提としています。

ただし、異なるバージョンの Visual Studio を使用する場合は、いくつかの違いを考慮する必要があり Entity Framework。

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15.7 以降

- このバージョンの Visual Studio には Entity Framework ツールと EF 6.2 ランタイムの最新リリースが含まれており、追加のセットアップ手順は必要ありません。
これらのリリースの詳細については、「 [新機能](xref:ef6/what-is-new/index) 」を参照してください。
- EF ツールを使用して新しいプロジェクトに Entity Framework を追加すると、EF 6.2 NuGet パッケージが自動的に追加されます。
オンラインで利用できる任意の EF NuGet パッケージに手動でインストールすることも、アップグレードすることもできます。
- 既定では、このバージョンの Visual Studio で使用できる SQL Server インスタンスは、MSSQLLocalDB という名前の LocalDB インスタンスです。
使用する必要がある接続文字列のサーバーセクションは、"(localdb) \\ MSSQLLocalDB" です。
`@` \\ \\ C# コードで接続文字列を指定する場合は、または二重のバックスラッシュ "" で始まる逐語的文字列を使用することを忘れないでください。  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 から Visual Studio 2017 15.6

- これらのバージョンの Visual Studio には Entity Framework ツールとランタイム ef6.1.3 が含まれています。
これらのリリースの詳細については、「 [過去のリリース](xref:ef6/what-is-new/past-releases#ef-613) 」を参照してください。
- EF ツールを使用して新しいプロジェクトに Entity Framework を追加すると、EF ef6.1.3 NuGet パッケージが自動的に追加されます。
オンラインで利用できる任意の EF NuGet パッケージに手動でインストールすることも、アップグレードすることもできます。
- 既定では、このバージョンの Visual Studio で使用できる SQL Server インスタンスは、MSSQLLocalDB という名前の LocalDB インスタンスです。
使用する必要がある接続文字列のサーバーセクションは、"(localdb) \\ MSSQLLocalDB" です。
`@` \\ \\ C# コードで接続文字列を指定する場合は、または二重のバックスラッシュ "" で始まる逐語的文字列を使用することを忘れないでください。  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- このバージョンの Visual Studio には、以前のバージョンの Entity Framework ツールとランタイムが含まれています。
Microsoft ダウンロードセンターで入手できる [インストーラー](https://www.microsoft.com/download/details.aspx?id=40762) を使用して、Entity Framework Tools ef6.1.3 にアップグレードすることをお勧めします。
これらのリリースの詳細については、「 [過去のリリース](xref:ef6/what-is-new/past-releases#ef-613) 」を参照してください。
- アップグレードした EF ツールを使用して新しいプロジェクトに Entity Framework を追加すると、EF ef6.1.3 NuGet パッケージが自動的に追加されます。
オンラインで利用できる任意の EF NuGet パッケージに手動でインストールすることも、アップグレードすることもできます。
- 既定では、このバージョンの Visual Studio で使用できる SQL Server インスタンスは、MSSQLLocalDB という名前の LocalDB インスタンスです。
使用する必要がある接続文字列のサーバーセクションは、"(localdb) \\ MSSQLLocalDB" です。
`@` \\ \\ C# コードで接続文字列を指定する場合は、または二重のバックスラッシュ "" で始まる逐語的文字列を使用することを忘れないでください。  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- このバージョンの Visual Studio には、以前のバージョンの Entity Framework ツールとランタイムが含まれています。
Microsoft ダウンロードセンターで入手できる [インストーラー](https://www.microsoft.com/download/details.aspx?id=40762) を使用して、Entity Framework Tools ef6.1.3 にアップグレードすることをお勧めします。
これらのリリースの詳細については、「 [過去のリリース](xref:ef6/what-is-new/past-releases#ef-613) 」を参照してください。
- アップグレードした EF ツールを使用して新しいプロジェクトに Entity Framework を追加すると、EF ef6.1.3 NuGet パッケージが自動的に追加されます。
オンラインで利用できる任意の EF NuGet パッケージに手動でインストールすることも、アップグレードすることもできます。
- 既定では、このバージョンの Visual Studio で使用できる SQL Server インスタンスは、v2.0 という名前の LocalDB インスタンスです。
使用する必要がある接続文字列のサーバーセクションは、"(localdb) v1.0 \\ " です。
`@` \\ \\ C# コードで接続文字列を指定する場合は、または二重のバックスラッシュ "" で始まる逐語的文字列を使用することを忘れないでください。  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- このバージョンの Visual Studio で使用可能なバージョンの Entity Framework Tools は Entity Framework 6 ランタイムと互換性がないため、アップグレードできません。
- 既定では、Entity Framework ツールによって Entity Framework 4.0 がプロジェクトに追加されます。
新しいバージョンの EF を使用してアプリケーションを作成するには、最初に [NuGet パッケージマネージャー拡張機能](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)をインストールする必要があります。
- 既定では、EF ツールのバージョンのすべてのコード生成は EntityObject と Entity Framework 4 に基づいています。
[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)または[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)の dbcontext コード生成テンプレートをインストールすることによって、dbcontext と Entity Framework 5 に基づくコード生成を切り替えることをお勧めします。
- NuGet パッケージマネージャーの拡張機能をインストールしたら、オンラインで利用可能な EF NuGet パッケージを手動でインストールするか、または EF6 と Code First 共に使用することができます。この場合、デザイナーは必要ありません。
- 既定では、このバージョンの Visual Studio で使用できる SQL Server インスタンスは、SQLEXPRESS という名前 SQL Server Express ます。
使用する必要がある接続文字列のサーバーセクションは "です。 \\SQLEXPRESS "。
`@` \\ \\ C# コードで接続文字列を指定する場合は、または二重のバックスラッシュ "" で始まる逐語的文字列を使用することを忘れないでください。
