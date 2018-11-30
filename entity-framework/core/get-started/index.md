---
title: 概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: b846d63f2c285a43d60eecfb2be3d460a5d31924
ms.sourcegitcommit: 064b09431f05848830e145a6cd65cad58881557c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52552595"
---
# <a name="getting-started-with-entity-framework-core"></a><span data-ttu-id="5c5b6-102">Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="5c5b6-102">Getting Started with Entity Framework Core</span></span>

## <a name="installing-ef-coreinstallindexmd"></a>[<span data-ttu-id="5c5b6-103">EF Core のインストール</span><span class="sxs-lookup"><span data-stu-id="5c5b6-103">Installing EF Core</span></span>](install/index.md)

<span data-ttu-id="5c5b6-104">さまざまなプラットフォームと人気の IDE でアプリケーションに EF Core を追加するために必要な手順のまとめ。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-104">A summary of the steps necessary to add EF Core to your application in different platforms and popular IDEs.</span></span>

## <a name="step-by-step-tutorials"></a><span data-ttu-id="5c5b6-105">ステップ バイ ステップのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="5c5b6-105">Step-by-step Tutorials</span></span>

<span data-ttu-id="5c5b6-106">これらの入門用チュートリアルでは、Entity Framework Core や特定の IDE に関する予備知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-106">These introductory tutorials require no previous knowledge of Entity Framework Core or any particular IDE.</span></span> <span data-ttu-id="5c5b6-107">そこでは、データベースを照会し、データを保存する単純なアプリケーションを、ステップ バイ ステップで作成していきます。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-107">They will take you step-by-step through the creation of a simple application that queries and saves data from a database.</span></span> <span data-ttu-id="5c5b6-108">Microsoft は、さまざまなシステムやアプリケーションで始めるためのチュートリアルを提供してきました。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-108">We have provided tutorials to get you started on various operating systems and application types.</span></span>

<span data-ttu-id="5c5b6-109">Entity Framework Core では、既存のデータベースに基づいてモデルを作成したり、自分のモデルに基づいてデータベースを自動作成させたりできます。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-109">Entity Framework Core can create a model based on an existing database, or create a database for you based on your model.</span></span> <span data-ttu-id="5c5b6-110">両方の手法を紹介するチュートリアルを用意しています。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-110">There are tutorials that demonstrate both of these approaches.</span></span>

* <span data-ttu-id="5c5b6-111">.NET Core コンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="5c5b6-111">.NET Core Console Apps</span></span>
  * [<span data-ttu-id="5c5b6-112">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-112">New Database</span></span>](netcore/new-db-sqlite.md)
* <span data-ttu-id="5c5b6-113">ASP.NET Core アプリ</span><span class="sxs-lookup"><span data-stu-id="5c5b6-113">ASP.NET Core Apps</span></span>
  * [<span data-ttu-id="5c5b6-114">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-114">New Database</span></span>](aspnetcore/new-db.md)
  * [<span data-ttu-id="5c5b6-115">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-115">Existing Database</span></span>](aspnetcore/existing-db.md)
  * [<span data-ttu-id="5c5b6-116">EF Core と Razor ページ</span><span class="sxs-lookup"><span data-stu-id="5c5b6-116">EF Core and Razor Pages</span></span>](/aspnet/core/data/ef-rp/intro)
* <span data-ttu-id="5c5b6-117">ユニバーサル Windows プラットフォーム (UWP) アプリ</span><span class="sxs-lookup"><span data-stu-id="5c5b6-117">Universal Windows Platform (UWP) Apps</span></span>
  * [<span data-ttu-id="5c5b6-118">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-118">New Database</span></span>](uwp/getting-started.md)
* <span data-ttu-id="5c5b6-119">.NET Framework アプリ</span><span class="sxs-lookup"><span data-stu-id="5c5b6-119">.NET Framework Apps</span></span>
  * [<span data-ttu-id="5c5b6-120">新しいデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-120">New Database</span></span>](full-dotnet/new-db.md)
  * [<span data-ttu-id="5c5b6-121">既存のデータベース</span><span class="sxs-lookup"><span data-stu-id="5c5b6-121">Existing Database</span></span>](full-dotnet/existing-db.md)

> [!NOTE]  
> <span data-ttu-id="5c5b6-122">これらのチュートリアルと付属のサンプルは、EF Core 2.1 を使用するために更新されました。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-122">These tutorials and the accompanying samples have been updated to use EF Core 2.1.</span></span> <span data-ttu-id="5c5b6-123">ただし、ほとんどの場合、命令を最小限変更するだけで、以前のリリースを使用するアプリケーションを作成できるはずです。</span><span class="sxs-lookup"><span data-stu-id="5c5b6-123">However, in the majority of cases it should be possible to create applications that use previous releases, with minimal modification to the instructions.</span></span> 
