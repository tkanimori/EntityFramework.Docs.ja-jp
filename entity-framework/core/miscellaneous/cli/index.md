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
<a name="entity-framework-core-tools"></a><span data-ttu-id="0b603-102">Entity Framework Core ツール</span><span class="sxs-lookup"><span data-stu-id="0b603-102">Entity Framework Core Tools</span></span>
===========================
<span data-ttu-id="0b603-103">Entity Framework Core ツールは、EF Core アプリの開発で役立ちます。</span><span class="sxs-lookup"><span data-stu-id="0b603-103">The Entity Framework Core Tools help you during the development of EF Core apps.</span></span> <span data-ttu-id="0b603-104">DbContext とエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) と移行の管理に主に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b603-104">They're primarily used to scaffold a DbContext and entity types by reverse engineering the schema of a database, and to manage Migrations.</span></span>

<span data-ttu-id="0b603-105">[EF Core パッケージ マネージャー コンソール (PMC) ツール][1]を利用すると、Visual Studio がより使いやすくなります。</span><span class="sxs-lookup"><span data-stu-id="0b603-105">The [EF Core Package Manager Console (PMC) Tools][1] provide a superior experience inside Visual Studio.</span></span> <span data-ttu-id="0b603-106">NuGet の[パッケージ マネージャー コンソール][2]を利用して実行します。</span><span class="sxs-lookup"><span data-stu-id="0b603-106">Run them using NuGet's [Package Manager Console][2].</span></span> <span data-ttu-id="0b603-107">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="0b603-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="0b603-108">[EF Core .NET コマンド ライン ツール][3]は、プラットフォームに依存せず、Visual Studio の外で実行できる [.NET Core コマンドライン インターフェイス (CLI) ツール][4]の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="0b603-108">The [EF Core .NET Command-line Tools][3] are an extension to the [.NET Core command-line interface (CLI) tools][4] that are cross-platform and can run outside of Visual Studio.</span></span> <span data-ttu-id="0b603-109">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="0b603-109">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="0b603-110">いずれのツールも機能は同じです。</span><span class="sxs-lookup"><span data-stu-id="0b603-110">Both tools expose the same functionality.</span></span> <span data-ttu-id="0b603-111">Visual Studio で開発している場合、統合性に優れた PMC ツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0b603-111">If you're developing in Visual Studio, we recommend using the PMC Tools since they provide a more integrated experience.</span></span>

<a name="frameworks"></a><span data-ttu-id="0b603-112">フレームワーク</span><span class="sxs-lookup"><span data-stu-id="0b603-112">Frameworks</span></span>
----------
<span data-ttu-id="0b603-113">このツールは、.NET Framework または .NET Core を対象とするプロジェクトをサポートします。</span><span class="sxs-lookup"><span data-stu-id="0b603-113">The tools support projects targeting .NET Framework or .NET Core.</span></span>

<span data-ttu-id="0b603-114">クラス ライブラリを使用する場合、可能であれば .NET Core または .NET Framework の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="0b603-114">If you want to use a class library, then consider using a .NET Core or .NET Framework class library if possible.</span></span> <span data-ttu-id="0b603-115">これにより、.NET ツールで発生する問題が最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="0b603-115">This will result in the least issues with .NET tooling.</span></span> <span data-ttu-id="0b603-116">代わりに .NET Standard クラス ライブラリを使用する場合は、クラス ライブラリに読み込むことができる具体的なターゲット プラットフォームがツールに含まれるように、.NET Framework または .NET Core を対象とするスタートアップ プロジェクトを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0b603-116">If instead you wish to use a .NET Standard class library, then you will need to use a startup project that targets .NET Framework or .NET Core so that the tooling has a conrete target platform into which it can load your class library.</span></span> <span data-ttu-id="0b603-117">このスタートアップ プロジェクトは実際のコードを持たないダミー プロジェクトにすることができ、ツールのターゲットを提供するためだけに必要とされます。</span><span class="sxs-lookup"><span data-stu-id="0b603-117">This startup project can be a dummy project with no real code--it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="0b603-118">プロジェクトが別のフレームワーク (ユニバーサル Windows や Xamarin など) を対象としている場合、個別の .NET Standard クラス ライブラリを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0b603-118">If your project targets another framework (for example, Universal Windows or Xamarin), then you will need to create a separate .NET Standard class library.</span></span> <span data-ttu-id="0b603-119">この場合、上記の指示に従って、ツールで使用できるスタートアップ プロジェクトも作成してください。</span><span class="sxs-lookup"><span data-stu-id="0b603-119">In this case, follow the guidance above to also create a startup project that can be used by the tooling.</span></span>

<a name="startup-and-target-projects"></a><span data-ttu-id="0b603-120">スタートアップ プロジェクトとターゲット プロジェクト</span><span class="sxs-lookup"><span data-stu-id="0b603-120">Startup and Target Projects</span></span>
---------------------------
<span data-ttu-id="0b603-121">コマンドの呼び出しでは 2 つのプロジェクトが関連します。ターゲット プロジェクトとスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="0b603-121">Whenever you invoke a command, there are two projects involved: the target project and the startup project.</span></span>

<span data-ttu-id="0b603-122">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="0b603-122">The target project is where any files are added (or in some cases removed).</span></span>

<span data-ttu-id="0b603-123">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="0b603-123">The startup project is the one emulated by the tools when executing your project's code.</span></span>

<span data-ttu-id="0b603-124">ターゲット プロジェクトとスタートアップ プロジェクトは同じものにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0b603-124">Both the target project and the startup project can be the same.</span></span>


  [1]: powershell.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: dotnet.md
  [4]: https://docs.microsoft.com/dotnet/core/tools/
