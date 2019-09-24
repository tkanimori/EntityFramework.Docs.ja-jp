---
title: 新機能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149117"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="8232d-102">EF6 の新機能</span><span class="sxs-lookup"><span data-stu-id="8232d-102">What's New in EF6</span></span>

<span data-ttu-id="8232d-103">最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8232d-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="8232d-104">ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。</span><span class="sxs-lookup"><span data-stu-id="8232d-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="8232d-105">特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8232d-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="8232d-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="8232d-106">EF 6.3.0</span></span>

<span data-ttu-id="8232d-107">EF 6.3.0 ランタイムは、NuGet で 2019 年 9 月にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="8232d-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="8232d-108">このリリースの主な目的は、EF 6 を使用する既存のアプリケーションを .NET Core 3.0 に円滑に移行することです。</span><span class="sxs-lookup"><span data-stu-id="8232d-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="8232d-109">コミュニティによって、いくつかのバグ修正と機能強化も提供されています。</span><span class="sxs-lookup"><span data-stu-id="8232d-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="8232d-110">詳細については、各 [6.3.0](https://github.com/aspnet/EntityFramework6/milestones?state=closed) マイルストーンで解決されたイシューを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8232d-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="8232d-111">次に、重要なものをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="8232d-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="8232d-112">.NET Core 3.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="8232d-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="8232d-113">EntityFramework パッケージでは、.NET Framework 4.x に加え .NET Standard 2.1 をターゲットとするようになりました。</span><span class="sxs-lookup"><span data-stu-id="8232d-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x</span></span>
  - <span data-ttu-id="8232d-114">移行コマンドは、アウトプロセスの実行と SDK 形式のプロジェクトで動作するよう、書き替えられています。</span><span class="sxs-lookup"><span data-stu-id="8232d-114">The migrations commands have been rewritten to execute out of process and work with SDK-style projects</span></span>
- <span data-ttu-id="8232d-115">SQL Server HierarchyId のサポート</span><span class="sxs-lookup"><span data-stu-id="8232d-115">Support for SQL Server HierarchyId</span></span>
- <span data-ttu-id="8232d-116">Roslyn と NuGet PackageReference との互換性の向上</span><span class="sxs-lookup"><span data-stu-id="8232d-116">Improved compatibility with Roslyn and NuGet PackageReference</span></span>
- <span data-ttu-id="8232d-117">アセンブリからの移行を有効化、追加、スクリプト作成、および適用する ef6.exe を追加しました。</span><span class="sxs-lookup"><span data-stu-id="8232d-117">Added ef6.exe for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="8232d-118">これは、migrate.exe の代わりです</span><span class="sxs-lookup"><span data-stu-id="8232d-118">This replaces migrate.exe</span></span>

## <a name="past-releases"></a><span data-ttu-id="8232d-119">以前のリリース</span><span class="sxs-lookup"><span data-stu-id="8232d-119">Past Releases</span></span>

<span data-ttu-id="8232d-120">[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8232d-120">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
