---
title: コマンド ライン リファレンス - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/06/2017
ms.openlocfilehash: d43b01fc61bb1c9b678e12e41c27d7efe9a59fa5
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490364"
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

クラス ライブラリを使用する場合、可能であれば .NET Core または .NET Framework の使用を検討してください。 これにより、.NET ツールで発生する問題が最小限に抑えられます。 代わりに .NET Standard クラス ライブラリを使用する場合は、クラス ライブラリに読み込むことができる具体的なターゲット プラットフォームがツールに含まれるように、.NET Framework または .NET Core を対象とするスタートアップ プロジェクトを使用する必要があります。 このスタートアップ プロジェクトは実際のコードを持たないダミー プロジェクトにすることができ、ツールのターゲットを提供するためだけに必要とされます。

プロジェクトが別のフレームワーク (ユニバーサル Windows や Xamarin など) を対象としている場合、個別の .NET Standard クラス ライブラリを作成する必要があります。 この場合、上記の指示に従って、ツールで使用できるスタートアップ プロジェクトも作成してください。

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
