---
title: 概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: c8d53b47d215c0db673c9058e9d78a7e2e7b895f
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250739"
---
# <a name="getting-started-with-entity-framework-core"></a><span data-ttu-id="425f8-102">Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="425f8-102">Getting Started with Entity Framework Core</span></span>

## <a name="installing-ef-coreinstallindexmd"></a>[<span data-ttu-id="425f8-103">EF Core のインストール</span><span class="sxs-lookup"><span data-stu-id="425f8-103">Installing EF Core</span></span>](install/index.md)

<span data-ttu-id="425f8-104">さまざまなプラットフォームと人気の IDE でアプリケーションに EF Core を追加するために必要な手順のまとめ。</span><span class="sxs-lookup"><span data-stu-id="425f8-104">A summary of the steps necessary to add EF Core to your application in different platforms and popular IDEs.</span></span>

## <a name="step-by-step-tutorials"></a><span data-ttu-id="425f8-105">ステップ バイ ステップのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="425f8-105">Step-by-step Tutorials</span></span>

<span data-ttu-id="425f8-106">これらの入門用チュートリアルでは、Entity Framework Core や特定の IDE に関する予備知識が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="425f8-106">These introductory tutorials require no previous knowledge of Entity Framework Core or a particular IDE.</span></span> <span data-ttu-id="425f8-107">データベースにデータを問い合わせ、データを保存する単純なアプリケーションを段階的に作成します。</span><span class="sxs-lookup"><span data-stu-id="425f8-107">They will take you step-by-step through creating a simple application that queries and saves data from a database.</span></span> <span data-ttu-id="425f8-108">Microsoft は、さまざまなシステムやアプリケーションで始めるためのチュートリアルを提供してきました。</span><span class="sxs-lookup"><span data-stu-id="425f8-108">We have provided tutorials to get you started on various operating systems and application types.</span></span>

<span data-ttu-id="425f8-109">Entity Framework Core では、既存のデータベースに基づいてモデルを作成したり、自分のモデルに基づいてデータベースを自動作成させたりできます。</span><span class="sxs-lookup"><span data-stu-id="425f8-109">Entity Framework Core can create a model based on an existing database, or create a database for you based on your model.</span></span> <span data-ttu-id="425f8-110">両方の手法を紹介するチュートリアルを用意しています。</span><span class="sxs-lookup"><span data-stu-id="425f8-110">There are tutorials that demonstrate both of these approaches.</span></span>

* <span data-ttu-id="425f8-111">.NET framework (コンソール アプリ、WinForms、WPF)</span><span class="sxs-lookup"><span data-stu-id="425f8-111">.NET Framework (Console apps, WinForms, WPF)</span></span>
  * [<span data-ttu-id="425f8-112">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-112">New Database</span></span>](full-dotnet/new-db.md)
  * [<span data-ttu-id="425f8-113">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-113">Existing Database</span></span>](full-dotnet/existing-db.md)
* <span data-ttu-id="425f8-114">.NET core (Windows、macOS、Linux)</span><span class="sxs-lookup"><span data-stu-id="425f8-114">.NET Core (Windows, macOS, Linux)</span></span>
  * [<span data-ttu-id="425f8-115">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-115">New Database</span></span>](netcore/new-db-sqlite.md)
* <span data-ttu-id="425f8-116">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="425f8-116">ASP.NET Core</span></span>
  * [<span data-ttu-id="425f8-117">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-117">New Database</span></span>](aspnetcore/new-db.md)
  * [<span data-ttu-id="425f8-118">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-118">Existing Database</span></span>](aspnetcore/existing-db.md)
  * [<span data-ttu-id="425f8-119">EF Core と Razor ページ</span><span class="sxs-lookup"><span data-stu-id="425f8-119">EF Core and Razor Pages</span></span>](/aspnet/core/data/ef-rp/intro)
* <span data-ttu-id="425f8-120">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="425f8-120">Universal Windows Platform (UWP)</span></span>
  * [<span data-ttu-id="425f8-121">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="425f8-121">New Database</span></span>](uwp/getting-started.md)

> [!NOTE]  
> <span data-ttu-id="425f8-122">これらのチュートリアルと付属のサンプルは、EF Core 2.1 を使用するために更新されました。</span><span class="sxs-lookup"><span data-stu-id="425f8-122">These tutorials and the accompanying samples have been updated to use EF Core 2.1.</span></span> <span data-ttu-id="425f8-123">ただし、ほとんどの場合、命令を最小限変更するだけで、以前のリリースを使用するアプリケーションを作成できるはずです。</span><span class="sxs-lookup"><span data-stu-id="425f8-123">However, in the majority of cases it should be possible to create applications that use previous releases, with minimal modification to the instructions.</span></span> 
