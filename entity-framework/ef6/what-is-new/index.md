---
title: 新機能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: e0367aeefd682434bf520301776bcff4f0e72e06
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136136"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="cf611-102">EF6 の新機能</span><span class="sxs-lookup"><span data-stu-id="cf611-102">What's new in EF6</span></span>

<span data-ttu-id="cf611-103">最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf611-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="cf611-104">ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。</span><span class="sxs-lookup"><span data-stu-id="cf611-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="cf611-105">特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf611-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-640"></a><span data-ttu-id="cf611-106">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="cf611-106">EF 6.4.0</span></span>

<span data-ttu-id="cf611-107">EF 6.4.0 ランタイムは、NuGet で 2019 年 12 月にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="cf611-107">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="cf611-108">EF 6.4 の主な目的は、EF 6.3 で提供された機能とシナリオをポーランド語にすることです。</span><span class="sxs-lookup"><span data-stu-id="cf611-108">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="cf611-109">Github にある[重要な修正の一覧](https://github.com/dotnet/ef6/milestone/14?closed=1)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf611-109">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="cf611-110">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="cf611-110">EF 6.3.0</span></span>

<span data-ttu-id="cf611-111">EF 6.3.0 ランタイムは、NuGet で 2019 年 9 月にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="cf611-111">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="cf611-112">このリリースの主な目的は、EF 6 を使用する既存のアプリケーションを .NET Core 3.0 に円滑に移行することです。</span><span class="sxs-lookup"><span data-stu-id="cf611-112">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="cf611-113">コミュニティによって、いくつかのバグ修正と機能強化も提供されています。</span><span class="sxs-lookup"><span data-stu-id="cf611-113">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="cf611-114">詳細については、各 [6.3.0](https://github.com/aspnet/EntityFramework6/milestones?state=closed) マイルストーンで解決されたイシューを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf611-114">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="cf611-115">次に、重要なものをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="cf611-115">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="cf611-116">.NET Core 3.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="cf611-116">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="cf611-117">EntityFramework パッケージでは、.NET Framework 4.x に加え .NET Standard 2.1 をターゲットとするようになりました。</span><span class="sxs-lookup"><span data-stu-id="cf611-117">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="cf611-118">これは、EF 6.3 がクロスプラットフォームで、Linux や macOS などの Windows 以外のオペレーティングシステムでサポートされることを意味します。</span><span class="sxs-lookup"><span data-stu-id="cf611-118">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="cf611-119">移行コマンドは、アウト プロセスの実行と SDK 形式のプロジェクトで動作するように書き替えられています。</span><span class="sxs-lookup"><span data-stu-id="cf611-119">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="cf611-120">SQL Server HierarchyId のサポート。</span><span class="sxs-lookup"><span data-stu-id="cf611-120">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="cf611-121">Roslyn と NuGet PackageReference との互換性の向上。</span><span class="sxs-lookup"><span data-stu-id="cf611-121">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="cf611-122">アセンブリからの移行を有効化、追加、スクリプト作成、および適用する `ef6.exe` ユーティリティが追加されました。</span><span class="sxs-lookup"><span data-stu-id="cf611-122">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="cf611-123">これにより `migrate.exe` が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf611-123">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="cf611-124">EF デザイナーのサポート</span><span class="sxs-lookup"><span data-stu-id="cf611-124">EF designer support</span></span>

<span data-ttu-id="cf611-125">現在、.NET Core または .NET Standard のプロジェクトでも、SDK スタイルの .NET Framework プロジェクトでも、EF デザイナーを直接使用することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cf611-125">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="cf611-126">この制限を回避するには、エンティティと DbContext の EDMX ファイルと生成されたクラスを、リンク ファイルとして、同じソリューション内の .NET Core 3.0 または .NET Standard 2.1 プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf611-126">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="cf611-127">そのリンク ファイルは、プロジェクト ファイル内で次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cf611-127">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="cf611-128">EDMX ファイルが EntityDeploy ビルド アクションにリンクされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf611-128">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="cf611-129">これは特別な MSBuild タスク (EF 6.3 パッケージに含まれるようになりました) で、EF モデルを埋め込みリソースとして対象のアセンブリに追加する処理 (または、EDMX の [メタデータ成果物の処理] 設定に基づいて、これをファイルとして出力フォルダーにコピーする処理) を担当します。</span><span class="sxs-lookup"><span data-stu-id="cf611-129">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="cf611-130">これを設定する方法について詳しくは、[EDMX .NET Core サンプル](https://aka.ms/EdmxDotNetCoreSample)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf611-130">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="cf611-131">警告: "実際の" .edmx ファイルを定義している古いスタイル (つまり、非 SDK スタイル) の .NET Framework プロジェクトは必ず、.sln ファイル内のリンクを定義するプロジェクトの_前_にくるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cf611-131">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="cf611-132">そうしないと、デザイナーで .edmx ファイルを開いたとき、次のエラー メッセージが表示されます: "現在、プロジェクトに対して指定されているターゲット フレームワークでは Entity Framework が使用できません。</span><span class="sxs-lookup"><span data-stu-id="cf611-132">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="cf611-133">プロジェクトのターゲット フレームワークを変更することも、XmlEditor でモデルを編集することもできます"。</span><span class="sxs-lookup"><span data-stu-id="cf611-133">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="cf611-134">以前のリリース</span><span class="sxs-lookup"><span data-stu-id="cf611-134">Past Releases</span></span>

<span data-ttu-id="cf611-135">[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf611-135">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
