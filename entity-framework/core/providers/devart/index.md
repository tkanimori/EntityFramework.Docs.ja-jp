---
title: "Devart データ プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: aad70a75-d04d-4d63-a489-7f9062a3c73d
ms.technology: entity-framework-core
uid: core/providers/devart/index
ms.openlocfilehash: 04de917b3327a6f544292781bca42a1170c199ad
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="devart-ef-core-database-providers"></a><span data-ttu-id="b4a14-102">Devart EF Core データ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b4a14-102">Devart EF Core Database Providers</span></span>

<span data-ttu-id="b4a14-103">Devart は、さまざまなデータベースのデータベース プロバイダーを提供するサードパーティ プロバイダー ライターです。</span><span class="sxs-lookup"><span data-stu-id="b4a14-103">Devart is a third party provider writer that offers database providers for a wide range of databases.</span></span> <span data-ttu-id="b4a14-104">詳細については、[devart.com/dotconnect](https://www.devart.com/dotconnect/) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4a14-104">Find out more at [devart.com/dotconnect](https://www.devart.com/dotconnect/).</span></span>

> [!NOTE]  
> <span data-ttu-id="b4a14-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="b4a14-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="b4a14-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="b4a14-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="paid-versions-only"></a><span data-ttu-id="b4a14-107">有料バージョンのみ</span><span class="sxs-lookup"><span data-stu-id="b4a14-107">Paid Versions Only</span></span>

<span data-ttu-id="b4a14-108">Devart dotConnect は商用サードパーティ プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="b4a14-108">Devart dotConnect is a commercial third party provider.</span></span> <span data-ttu-id="b4a14-109">Entity Framework サポートは、有料版の dotConnect でのみご利用いただけます。</span><span class="sxs-lookup"><span data-stu-id="b4a14-109">Entity Framework support is only available in paid versions of dotConnect.</span></span>

## <a name="install"></a><span data-ttu-id="b4a14-110">Install</span><span class="sxs-lookup"><span data-stu-id="b4a14-110">Install</span></span>

<span data-ttu-id="b4a14-111">インストール方法については、[Devart dotConnect ドキュメント](https://www.devart.com/dotconnect/) ページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b4a14-111">See the [Devart dotConnect documentation](https://www.devart.com/dotconnect/) for installation instructions.</span></span>

## <a name="get-started"></a><span data-ttu-id="b4a14-112">開始するには</span><span class="sxs-lookup"><span data-stu-id="b4a14-112">Get Started</span></span>

<span data-ttu-id="b4a14-113">開始するにあたり、[Devart dotConnect Entity Framework ドキュメント](https://www.devart.com/dotconnect/entityframework.html)と [Entity Framework Core 1 サポートに関するブログ記事](http://blog.devart.com/entity-framework-core-1-entity-framework-7-support.html)をお読みください。</span><span class="sxs-lookup"><span data-stu-id="b4a14-113">See the [Devart dotConnect Entity Framework documentation](https://www.devart.com/dotconnect/entityframework.html) and [blog article about Entity Framework Core 1 Support](http://blog.devart.com/entity-framework-core-1-entity-framework-7-support.html) to get started.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="b4a14-114">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="b4a14-114">Supported Database Engines</span></span>

* <span data-ttu-id="b4a14-115">MySQL</span><span class="sxs-lookup"><span data-stu-id="b4a14-115">MySQL</span></span>

* <span data-ttu-id="b4a14-116">Oracle</span><span class="sxs-lookup"><span data-stu-id="b4a14-116">Oracle</span></span>

* <span data-ttu-id="b4a14-117">PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="b4a14-117">PostgreSQL</span></span>

* <span data-ttu-id="b4a14-118">SQLite</span><span class="sxs-lookup"><span data-stu-id="b4a14-118">SQLite</span></span>

* <span data-ttu-id="b4a14-119">DB2</span><span class="sxs-lookup"><span data-stu-id="b4a14-119">DB2</span></span>

* [<span data-ttu-id="b4a14-120">クラウド アプリ</span><span class="sxs-lookup"><span data-stu-id="b4a14-120">Cloud apps</span></span>](https://www.devart.com/dotconnect/#cloud)

## <a name="supported-platforms"></a><span data-ttu-id="b4a14-121">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="b4a14-121">Supported Platforms</span></span>

* <span data-ttu-id="b4a14-122">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="b4a14-122">.NET Framework (4.5.1 onwards)</span></span>
* <span data-ttu-id="b4a14-123">.NET Core 1.0 以降 (Oracle、MySQL、PostgreSQL、SQLite のプロバイダー)</span><span class="sxs-lookup"><span data-stu-id="b4a14-123">.NET Core 1.0 and higher (providers for Oracle, MySQL, PostgreSQL, SQLite)</span></span>
