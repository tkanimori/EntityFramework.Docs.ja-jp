---
title: "コマンド ライン リファレンス - EF Core"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 076e9251850ba10df323cd25922aa8b95b3a5491
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="entity-framework-core-tools"></a>Entity Framework Core ツール
===========================
Entity Framework Core ツールは、EF Core アプリの開発で役立ちます。 DbContext とエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) と移行の管理に主に使用されます。

[EF Core パッケージ マネージャー コンソール (PMC) ツール][1]を利用すると、Visual Studio がより使いやすくなります。 NuGet の[パッケージ マネージャー コンソール][2]を利用して実行します。 これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。

[EF Core .NET コマンド ライン ツール][3]は、プラットフォームに依存せず、Visual Studio の外で実行できる [.NET Core コマンドライン インターフェイス (CLI) ツール][4]の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

いずれのツールも機能は同じです。 Visual Studio で開発している場合、統合性に優れた PMC ツールの使用をお勧めします。

<a name="frameworks"></a>フレームワーク
----------
このツールは、.NET Framework または .NET Core を対象とするプロジェクトをサポートします。

プロジェクトが別のフレームワーク (Universal Windows や Xamarin など) を対象としている場合、別個の .NET Standard プロジェクトを作成し、サポートされているフレームワークの 1 つをクロスターゲットすることをお勧めします。

たとえば、.NET Core をクロスターゲットするには、プロジェクトを右クリックし、**[Edit \*.csproj]\(*csproj の編集\)** を選択します。 `TargetFramework` プロパティを次のように更新します。 (プロパティ名は複数形になります。)

``` xml
<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>
```

.NET Standard クラス ライブラリを使用している場合、スタートアップ プロジェクトのターゲットが .NET Framework または .NET Core であれば、クロスターゲットの必要はありません。

<a name="startup-and-target-projects"></a>スタートアップ プロジェクトとターゲット プロジェクト
---------------------------
コマンドの呼び出しでは 2 つのプロジェクトが関連します。ターゲット プロジェクトとスタートアップ プロジェクトです。

ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。

スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。

ターゲット プロジェクトとスタートアップ プロジェクトは同じものにすることができます。


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
