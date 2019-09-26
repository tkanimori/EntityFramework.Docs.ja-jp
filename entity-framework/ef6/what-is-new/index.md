---
title: 新機能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: bb7038764644682c2149a8a500f342804d01f3d2
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198045"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="25419-102">EF6 の新機能</span><span class="sxs-lookup"><span data-stu-id="25419-102">What's new in EF6</span></span>

<span data-ttu-id="25419-103">最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="25419-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="25419-104">ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。</span><span class="sxs-lookup"><span data-stu-id="25419-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="25419-105">特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25419-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="25419-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="25419-106">EF 6.3.0</span></span>

<span data-ttu-id="25419-107">EF 6.3.0 ランタイムは、NuGet で 2019 年 9 月にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="25419-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="25419-108">このリリースの主な目的は、EF 6 を使用する既存のアプリケーションを .NET Core 3.0 に円滑に移行することです。</span><span class="sxs-lookup"><span data-stu-id="25419-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="25419-109">コミュニティによって、いくつかのバグ修正と機能強化も提供されています。</span><span class="sxs-lookup"><span data-stu-id="25419-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="25419-110">詳細については、各 [6.3.0](https://github.com/aspnet/EntityFramework6/milestones?state=closed) マイルストーンで解決されたイシューを参照してください。</span><span class="sxs-lookup"><span data-stu-id="25419-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="25419-111">次に、重要なものをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="25419-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="25419-112">.NET Core 3.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="25419-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="25419-113">EntityFramework パッケージでは、.NET Framework 4.x に加え .NET Standard 2.1 をターゲットとするようになりました。</span><span class="sxs-lookup"><span data-stu-id="25419-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x</span></span>
  - <span data-ttu-id="25419-114">移行コマンドは、アウトプロセスの実行と SDK 形式のプロジェクトで動作するよう、書き替えられています。</span><span class="sxs-lookup"><span data-stu-id="25419-114">The migrations commands have been rewritten to execute out of process and work with SDK-style projects</span></span>
- <span data-ttu-id="25419-115">SQL Server HierarchyId のサポート</span><span class="sxs-lookup"><span data-stu-id="25419-115">Support for SQL Server HierarchyId</span></span>
- <span data-ttu-id="25419-116">Roslyn と NuGet PackageReference との互換性の向上</span><span class="sxs-lookup"><span data-stu-id="25419-116">Improved compatibility with Roslyn and NuGet PackageReference</span></span>
- <span data-ttu-id="25419-117">アセンブリからの移行を有効化、追加、スクリプト作成、および適用する `ef6.exe` ユーティリティが追加されました。</span><span class="sxs-lookup"><span data-stu-id="25419-117">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="25419-118">これにより `migrate.exe` が置き換えられます</span><span class="sxs-lookup"><span data-stu-id="25419-118">This replaces `migrate.exe`</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="25419-119">EF デザイナーのサポート</span><span class="sxs-lookup"><span data-stu-id="25419-119">EF designer support</span></span>

<span data-ttu-id="25419-120">現在、.NET Core または .NET Standard のプロジェクトで EF デザイナーを直接使用することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="25419-120">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects.</span></span> 

<span data-ttu-id="25419-121">この制限を回避するには、エンティティと DbContext の EDMX ファイルと生成されたクラスを、リンク ファイルとして、同じソリューション内の .NET Core 3.0 または .NET Standard 2.1 プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="25419-121">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="25419-122">そのリンク ファイルは、プロジェクト ファイル内で次のようになります。</span><span class="sxs-lookup"><span data-stu-id="25419-122">The linked files will look like this in the project file:</span></span>

``` csproj 
&lt;ItemGroup&gt;
  &lt;EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" /&gt;
  &lt;Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" /&gt;
  &lt;Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" /&gt;
  &lt;Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" /&gt;
&lt;/ItemGroup&gt;
```

<span data-ttu-id="25419-123">EDMX ファイルが EntityDeploy ビルド アクションにリンクされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="25419-123">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="25419-124">これは特別な MSBuild タスク (EF 6.3 パッケージに含まれるようになりました) で、EF モデルを埋め込みリソースとして対象のアセンブリに追加する処理 (または、EDMX の [メタデータ成果物の処理] 設定に基づいて、これをファイルとして出力フォルダーにコピーする処理) を担当します。</span><span class="sxs-lookup"><span data-stu-id="25419-124">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="25419-125">これを設定する方法について詳しくは、[EDMX .NET Core サンプル](https://aka.ms/EdmxDotNetCoreSample)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="25419-125">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

## <a name="past-releases"></a><span data-ttu-id="25419-126">以前のリリース</span><span class="sxs-lookup"><span data-stu-id="25419-126">Past Releases</span></span>

<span data-ttu-id="25419-127">[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="25419-127">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
