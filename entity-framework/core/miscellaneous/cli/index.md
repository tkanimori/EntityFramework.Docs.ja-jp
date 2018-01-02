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
<a name="entity-framework-core-tools"></a><span data-ttu-id="e31e2-102">Entity Framework Core ツール</span><span class="sxs-lookup"><span data-stu-id="e31e2-102">Entity Framework Core Tools</span></span>
===========================
<span data-ttu-id="e31e2-103">Entity Framework Core ツールは、EF Core アプリの開発で役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e31e2-103">The Entity Framework Core Tools help you during the development of EF Core apps.</span></span> <span data-ttu-id="e31e2-104">DbContext とエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) と移行の管理に主に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e31e2-104">They're primarily used to scaffold a DbContext and entity types by reverse engineering the schema of a database, and to manage Migrations.</span></span>

<span data-ttu-id="e31e2-105">[EF Core パッケージ マネージャー コンソール (PMC) ツール][1]を利用すると、Visual Studio がより使いやすくなります。</span><span class="sxs-lookup"><span data-stu-id="e31e2-105">The [EF Core Package Manager Console (PMC) Tools][1] provide a superior experience inside Visual Studio.</span></span> <span data-ttu-id="e31e2-106">NuGet の[パッケージ マネージャー コンソール][2]を利用して実行します。</span><span class="sxs-lookup"><span data-stu-id="e31e2-106">Run them using NuGet's [Package Manager Console][2].</span></span> <span data-ttu-id="e31e2-107">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="e31e2-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="e31e2-108">[EF Core .NET コマンド ライン ツール][3]は、プラットフォームに依存せず、Visual Studio の外で実行できる [.NET Core コマンドライン インターフェイス (CLI) ツール][4]の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="e31e2-108">The [EF Core .NET Command-line Tools][3] are an extension to the [.NET Core command-line interface (CLI) tools][4] that are cross-platform and can run outside of Visual Studio.</span></span> <span data-ttu-id="e31e2-109">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="e31e2-109">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="e31e2-110">いずれのツールも機能は同じです。</span><span class="sxs-lookup"><span data-stu-id="e31e2-110">Both tools expose the same functionality.</span></span> <span data-ttu-id="e31e2-111">Visual Studio で開発している場合、統合性に優れた PMC ツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e31e2-111">If you're developing in Visual Studio, we recommend using the PMC Tools since they provide a more integrated experience.</span></span>

<a name="frameworks"></a><span data-ttu-id="e31e2-112">フレームワーク</span><span class="sxs-lookup"><span data-stu-id="e31e2-112">Frameworks</span></span>
----------
<span data-ttu-id="e31e2-113">このツールは、.NET Framework または .NET Core を対象とするプロジェクトをサポートします。</span><span class="sxs-lookup"><span data-stu-id="e31e2-113">The tools support projects targeting .NET Framework or .NET Core.</span></span>

<span data-ttu-id="e31e2-114">プロジェクトが別のフレームワーク (Universal Windows や Xamarin など) を対象としている場合、別個の .NET Standard プロジェクトを作成し、サポートされているフレームワークの 1 つをクロスターゲットすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e31e2-114">If your project targets another framework (for example, Universal Windows or Xamarin), we recommend creating a separate .NET Standard project and cross-targeting one of the supported frameworks.</span></span>

<span data-ttu-id="e31e2-115">たとえば、.NET Core をクロスターゲットするには、プロジェクトを右クリックし、**[Edit \*.csproj]\(*csproj の編集\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e31e2-115">To cross-target .NET Core, for example, right-click on the project and select **Edit \*.csproj**.</span></span> <span data-ttu-id="e31e2-116">`TargetFramework` プロパティを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="e31e2-116">Update the `TargetFramework` property as follows.</span></span> <span data-ttu-id="e31e2-117">(プロパティ名は複数形になります。)</span><span class="sxs-lookup"><span data-stu-id="e31e2-117">(Note, the property name becomes plural.)</span></span>

``` xml
<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>
```

<span data-ttu-id="e31e2-118">.NET Standard クラス ライブラリを使用している場合、スタートアップ プロジェクトのターゲットが .NET Framework または .NET Core であれば、クロスターゲットの必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e31e2-118">If you're using a .NET Standard class library, you don't need to cross-target if your startup project targets .NET Framework or .NET Core.</span></span>

<a name="startup-and-target-projects"></a><span data-ttu-id="e31e2-119">スタートアップ プロジェクトとターゲット プロジェクト</span><span class="sxs-lookup"><span data-stu-id="e31e2-119">Startup and Target Projects</span></span>
---------------------------
<span data-ttu-id="e31e2-120">コマンドの呼び出しでは 2 つのプロジェクトが関連します。ターゲット プロジェクトとスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="e31e2-120">Whenever you invoke a command, there are two projects involved: the target project and the startup project.</span></span>

<span data-ttu-id="e31e2-121">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="e31e2-121">The target project is where any files are added (or in some cases removed).</span></span>

<span data-ttu-id="e31e2-122">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="e31e2-122">The startup project is the one emulated by the tools when executing your project's code.</span></span>

<span data-ttu-id="e31e2-123">ターゲット プロジェクトとスタートアップ プロジェクトは同じものにすることができます。</span><span class="sxs-lookup"><span data-stu-id="e31e2-123">Both the target project and the startup project can be the same.</span></span>


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
