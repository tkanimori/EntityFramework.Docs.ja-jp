---
title: Entity Framework 6 - EF6 の概要
description: Entity Framework 6 の概要
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062790"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="a872b-103">Entity Framework 6 の概要</span><span class="sxs-lookup"><span data-stu-id="a872b-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="a872b-104">このガイドには、選択したドキュメント記事への一連のリンクと、すぐに使用を開始するためのチュートリアルとビデオが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a872b-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="a872b-105">基礎</span><span class="sxs-lookup"><span data-stu-id="a872b-105">Fundamentals</span></span>

* [<span data-ttu-id="a872b-106">Entity Framework を取得する</span><span class="sxs-lookup"><span data-stu-id="a872b-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="a872b-107">ここでは、お使いのアプリケーションに Entity Framework を追加し、EF デザイナーを使用する場合は、Visual Studio にそれを確実にインストールする方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="a872b-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="a872b-108">モデルの作成: Code First、EF デザイナー、EF ワークフロー</span><span class="sxs-lookup"><span data-stu-id="a872b-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="a872b-109">お使いの EF モデルに、コードの記述と、ボックスや線の描画のどちらを指定したいですか?</span><span class="sxs-lookup"><span data-stu-id="a872b-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="a872b-110">EF を使用して、オブジェクトを既存のデータベースにマップしますか? または EF を使用して自分のオブジェクトに合わせたデータベースを作成したいですか?</span><span class="sxs-lookup"><span data-stu-id="a872b-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="a872b-111">ここでは、EF6 を使用する次の 2 種類の方法について学習します: EF Designer と Code First。</span><span class="sxs-lookup"><span data-stu-id="a872b-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="a872b-112">説明に従って、違いに関するビデオをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a872b-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="a872b-113">DbContext の操作</span><span class="sxs-lookup"><span data-stu-id="a872b-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="a872b-114">DbContext は、使用方法を学習する必要のある最も重要な最初の EF 型です。</span><span class="sxs-lookup"><span data-stu-id="a872b-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="a872b-115">これは、データベース クエリのスタート パッドとして機能し、オブジェクトへの変更を追跡して、永続化してデータベースに戻せるようにします。</span><span class="sxs-lookup"><span data-stu-id="a872b-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="a872b-116">質問する</span><span class="sxs-lookup"><span data-stu-id="a872b-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="a872b-117">専門家のサポートを受け、コミュニティに自分の回答を投稿する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="a872b-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="a872b-118">投稿</span><span class="sxs-lookup"><span data-stu-id="a872b-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="a872b-119">Entity Framework 6 では、オープン開発モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="a872b-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="a872b-120">弊社の GitHub リポジトリにアクセスすることで、EF の機能向上にご協力いただける方法を紹介しています。</span><span class="sxs-lookup"><span data-stu-id="a872b-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="a872b-121">Code First リソース</span><span class="sxs-lookup"><span data-stu-id="a872b-121">Code First resources</span></span>

  - [<span data-ttu-id="a872b-122">既存のデータベース ワークフローへの Code First</span><span class="sxs-lookup"><span data-stu-id="a872b-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="a872b-123">新しいデータベース ワークフローへの Code First</span><span class="sxs-lookup"><span data-stu-id="a872b-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="a872b-124">Code First を使用した列挙型のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="a872b-125">Code First を使用した空間型のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="a872b-126">カスタム Code First 規約の記述</span><span class="sxs-lookup"><span data-stu-id="a872b-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="a872b-127">Visual Basic での Code First の Fluent 構成の使用</span><span class="sxs-lookup"><span data-stu-id="a872b-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="a872b-128">Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="a872b-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="a872b-129">チーム環境での Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="a872b-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="a872b-130">[自動の Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (これは推奨されなくなりました)</span><span class="sxs-lookup"><span data-stu-id="a872b-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="a872b-131">EF Designer リソース</span><span class="sxs-lookup"><span data-stu-id="a872b-131">EF Designer resources</span></span>
  - [<span data-ttu-id="a872b-132">Database First ワークフロー</span><span class="sxs-lookup"><span data-stu-id="a872b-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="a872b-133">Model First ワークフロー</span><span class="sxs-lookup"><span data-stu-id="a872b-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="a872b-134">列挙型のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="a872b-135">空間型のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="a872b-136">Table-Per Hierarchy 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="a872b-137">Table-Per Type 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="a872b-138">更新のためのストアド プロシージャ マッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="a872b-139">クエリのためのストアド プロシージャ マッピング</span><span class="sxs-lookup"><span data-stu-id="a872b-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="a872b-140">エンティティ分割</span><span class="sxs-lookup"><span data-stu-id="a872b-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="a872b-141">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="a872b-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="a872b-142">[定義クエリ](xref:ef6/modeling/designer/advanced/defining-query) (詳細)</span><span class="sxs-lookup"><span data-stu-id="a872b-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="a872b-143">[テーブル値関数](xref:ef6/modeling/designer/advanced/tvfs) (詳細)</span><span class="sxs-lookup"><span data-stu-id="a872b-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="a872b-144">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a872b-144">Other resources</span></span>
  - [<span data-ttu-id="a872b-145">非同期クエリと保存</span><span class="sxs-lookup"><span data-stu-id="a872b-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="a872b-146">WinForms とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="a872b-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="a872b-147">WPF とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="a872b-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="a872b-148">[自己追跡エンティティを使用した接続解除シナリオ](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (これは推奨されなくなりました)</span><span class="sxs-lookup"><span data-stu-id="a872b-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
