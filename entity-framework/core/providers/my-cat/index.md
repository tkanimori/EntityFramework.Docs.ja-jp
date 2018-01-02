---
title: "Pomelo MyCat データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/27/2017
ms.assetid: ad798f02-a7a4-45c1-b0a9-8e92f5dc6ff0
ms.technology: entity-framework-core
uid: core/providers/my-cat/index
ms.openlocfilehash: e13da3ab47e56d1b869e445f2398eda6ea84a79f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="pomelo-mycat-ef-core-database-provider"></a><span data-ttu-id="d62ad-102">Pomelo MyCat EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d62ad-102">Pomelo MyCat EF Core Database Provider</span></span>

<span data-ttu-id="d62ad-103">このデータベース プロバイダーにより、[MyCat](https://github.com/MyCATApache/Mycat-Server) と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d62ad-103">This database provider allows Entity Framework Core to be used with [MyCat](https://github.com/MyCATApache/Mycat-Server).</span></span> <span data-ttu-id="d62ad-104">このプロバイダーは [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="d62ad-104">The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy).</span></span>

> [!NOTE]  
> <span data-ttu-id="d62ad-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="d62ad-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="d62ad-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="d62ad-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="d62ad-107">Install</span><span class="sxs-lookup"><span data-stu-id="d62ad-107">Install</span></span>

<span data-ttu-id="d62ad-108">[プロジェクト サイトから最新のリリース](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy/releases)をダウンロードし、実行します。</span><span class="sxs-lookup"><span data-stu-id="d62ad-108">Download and run [the latest release from the project site](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy/releases).</span></span>

## <a name="get-started"></a><span data-ttu-id="d62ad-109">開始するには</span><span class="sxs-lookup"><span data-stu-id="d62ad-109">Get Started</span></span>

<span data-ttu-id="d62ad-110">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="d62ad-110">The following resources will help you get started with this provider.</span></span>
 * [<span data-ttu-id="d62ad-111">設定手順</span><span class="sxs-lookup"><span data-stu-id="d62ad-111">Setup steps</span></span>](https://github.com/aspnet/EntityFramework.Docs/issues/252)
 * [<span data-ttu-id="d62ad-112">入門ビデオ</span><span class="sxs-lookup"><span data-stu-id="d62ad-112">Getting started video</span></span>](https://www.youtube.com/watch?v=q0CXfFNtMZo)

## <a name="supported-database-engines"></a><span data-ttu-id="d62ad-113">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="d62ad-113">Supported Database Engines</span></span>

* <span data-ttu-id="d62ad-114">MyCat</span><span class="sxs-lookup"><span data-stu-id="d62ad-114">MyCat</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="d62ad-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="d62ad-115">Supported Platforms</span></span>

* <span data-ttu-id="d62ad-116">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="d62ad-116">.NET Framework (4.5.1 onwards)</span></span>
* <span data-ttu-id="d62ad-117">.NET Core</span><span class="sxs-lookup"><span data-stu-id="d62ad-117">.NET Core</span></span>
* <span data-ttu-id="d62ad-118">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="d62ad-118">Mono (4.2.0 onwards)</span></span>
