---
title: 概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 744ea587207775f3a5b9f7b14ba5959c55539c13
ms.sourcegitcommit: 39080d38e1adea90db741257e60dc0e7ed08aa82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980016"
---
# <a name="getting-started-with-entity-framework-core"></a><span data-ttu-id="1beb3-102">Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="1beb3-102">Getting Started with Entity Framework Core</span></span>

## <a name="installing-ef-coreinstallindexmd"></a>[<span data-ttu-id="1beb3-103">EF Core のインストール</span><span class="sxs-lookup"><span data-stu-id="1beb3-103">Installing EF Core</span></span>](install/index.md)

<span data-ttu-id="1beb3-104">さまざまなプラットフォームと人気の IDE でアプリケーションに EF Core を追加するために必要な手順のまとめ。</span><span class="sxs-lookup"><span data-stu-id="1beb3-104">A summary of the steps necessary to add EF Core to your application in different platforms and popular IDEs.</span></span>

## <a name="step-by-step-tutorials"></a><span data-ttu-id="1beb3-105">ステップ バイ ステップのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="1beb3-105">Step-by-step Tutorials</span></span>

<span data-ttu-id="1beb3-106">これらの入門用チュートリアルでは、Entity Framework Core や特定の IDE に関する予備知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1beb3-106">These introductory tutorials require no previous knowledge of Entity Framework Core or any particular IDE.</span></span> <span data-ttu-id="1beb3-107">そこでは、データベースを照会し、データを保存する単純なアプリケーションを、ステップ バイ ステップで作成していきます。</span><span class="sxs-lookup"><span data-stu-id="1beb3-107">They will take you step-by-step through the creation of a simple application that queries and saves data from a database.</span></span> <span data-ttu-id="1beb3-108">Microsoft は、さまざまなシステムやアプリケーションで始めるためのチュートリアルを提供してきました。</span><span class="sxs-lookup"><span data-stu-id="1beb3-108">We have provided tutorials to get you started on various operating systems and application types.</span></span>

<span data-ttu-id="1beb3-109">Entity Framework Core では、既存のデータベースに基づいてモデルを作成したり、自分のモデルに基づいてデータベースを自動作成させたりできます。</span><span class="sxs-lookup"><span data-stu-id="1beb3-109">Entity Framework Core can create a model based on an existing database, or create a database for you based on your model.</span></span> <span data-ttu-id="1beb3-110">両方の手法を紹介するチュートリアルを用意しています。</span><span class="sxs-lookup"><span data-stu-id="1beb3-110">There are tutorials that demonstrate both of these approaches.</span></span>

* <span data-ttu-id="1beb3-111">.NET framework (コンソール アプリ、WinForms、WPF)</span><span class="sxs-lookup"><span data-stu-id="1beb3-111">.NET Framework (Console apps, WinForms, WPF)</span></span>
  * [<span data-ttu-id="1beb3-112">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-112">New Database</span></span>](full-dotnet/new-db.md)
  * [<span data-ttu-id="1beb3-113">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-113">Existing Database</span></span>](full-dotnet/existing-db.md)
* <span data-ttu-id="1beb3-114">.NET core (Windows、macOS、Linux)</span><span class="sxs-lookup"><span data-stu-id="1beb3-114">.NET Core (Windows, macOS, Linux)</span></span>
  * [<span data-ttu-id="1beb3-115">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-115">New Database</span></span>](netcore/new-db-sqlite.md)
* <span data-ttu-id="1beb3-116">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1beb3-116">ASP.NET Core</span></span>
  * [<span data-ttu-id="1beb3-117">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-117">New Database</span></span>](aspnetcore/new-db.md)
  * [<span data-ttu-id="1beb3-118">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-118">Existing Database</span></span>](aspnetcore/existing-db.md)
  * [<span data-ttu-id="1beb3-119">EF Core と Razor ページ</span><span class="sxs-lookup"><span data-stu-id="1beb3-119">EF Core and Razor Pages</span></span>](/aspnet/core/data/ef-rp/intro)
* <span data-ttu-id="1beb3-120">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="1beb3-120">Universal Windows Platform (UWP)</span></span>
  * [<span data-ttu-id="1beb3-121">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="1beb3-121">New Database</span></span>](uwp/getting-started.md)

> [!NOTE]  
> <span data-ttu-id="1beb3-122">これらのチュートリアルと付属のサンプルは、EF Core 2.1 を使用するために更新されました。</span><span class="sxs-lookup"><span data-stu-id="1beb3-122">These tutorials and the accompanying samples have been updated to use EF Core 2.1.</span></span> <span data-ttu-id="1beb3-123">ただし、ほとんどの場合、命令を最小限変更するだけで、以前のリリースを使用するアプリケーションを作成できるはずです。</span><span class="sxs-lookup"><span data-stu-id="1beb3-123">However, in the majority of cases it should be possible to create applications that use previous releases, with minimal modification to the instructions.</span></span> 
