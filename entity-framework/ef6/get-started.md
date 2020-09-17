---
title: Entity Framework 6 - EF6 の概要
description: Entity Framework 6 の概要
author: divega
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: 825fe3716ade947f1660c8131100a1c3acba5a1d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072136"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="cd333-103">Entity Framework 6 の概要</span><span class="sxs-lookup"><span data-stu-id="cd333-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="cd333-104">このガイドには、選択したドキュメント記事への一連のリンクと、すぐに使用を開始するためのチュートリアルとビデオが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cd333-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="cd333-105">基礎</span><span class="sxs-lookup"><span data-stu-id="cd333-105">Fundamentals</span></span>

* [<span data-ttu-id="cd333-106">Entity Framework を取得する</span><span class="sxs-lookup"><span data-stu-id="cd333-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="cd333-107">ここでは、お使いのアプリケーションに Entity Framework を追加し、EF デザイナーを使用する場合は、Visual Studio にそれを確実にインストールする方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="cd333-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="cd333-108">モデルの作成: Code First、EF デザイナー、EF ワークフロー</span><span class="sxs-lookup"><span data-stu-id="cd333-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="cd333-109">お使いの EF モデルに、コードの記述と、ボックスや線の描画のどちらを指定したいですか?</span><span class="sxs-lookup"><span data-stu-id="cd333-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="cd333-110">EF を使用して、オブジェクトを既存のデータベースにマップしますか? または EF を使用して自分のオブジェクトに合わせたデータベースを作成したいですか?</span><span class="sxs-lookup"><span data-stu-id="cd333-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="cd333-111">ここでは、EF6 を使用する次の 2 種類の方法について学習します: EF Designer と Code First。</span><span class="sxs-lookup"><span data-stu-id="cd333-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="cd333-112">説明に従って、違いに関するビデオをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cd333-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="cd333-113">DbContext の操作</span><span class="sxs-lookup"><span data-stu-id="cd333-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="cd333-114">DbContext は、使用方法を学習する必要のある最も重要な最初の EF 型です。</span><span class="sxs-lookup"><span data-stu-id="cd333-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="cd333-115">これは、データベース クエリのスタート パッドとして機能し、オブジェクトへの変更を追跡して、永続化してデータベースに戻せるようにします。</span><span class="sxs-lookup"><span data-stu-id="cd333-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="cd333-116">質問する</span><span class="sxs-lookup"><span data-stu-id="cd333-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="cd333-117">専門家のサポートを受け、コミュニティに自分の回答を投稿する方法について説明しています。</span><span class="sxs-lookup"><span data-stu-id="cd333-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="cd333-118">投稿</span><span class="sxs-lookup"><span data-stu-id="cd333-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="cd333-119">Entity Framework 6 では、オープン開発モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="cd333-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="cd333-120">弊社の GitHub リポジトリにアクセスすることで、EF の機能向上にご協力いただける方法を紹介しています。</span><span class="sxs-lookup"><span data-stu-id="cd333-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="cd333-121">Code First リソース</span><span class="sxs-lookup"><span data-stu-id="cd333-121">Code First resources</span></span>

  - [<span data-ttu-id="cd333-122">既存のデータベース ワークフローへの Code First</span><span class="sxs-lookup"><span data-stu-id="cd333-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="cd333-123">新しいデータベース ワークフローへの Code First</span><span class="sxs-lookup"><span data-stu-id="cd333-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="cd333-124">Code First を使用した列挙型のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="cd333-125">Code First を使用した空間型のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="cd333-126">カスタム Code First 規約の記述</span><span class="sxs-lookup"><span data-stu-id="cd333-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="cd333-127">Visual Basic での Code First の Fluent 構成の使用</span><span class="sxs-lookup"><span data-stu-id="cd333-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="cd333-128">Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="cd333-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="cd333-129">チーム環境での Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="cd333-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="cd333-130">[自動の Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (これは推奨されなくなりました)</span><span class="sxs-lookup"><span data-stu-id="cd333-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="cd333-131">EF Designer リソース</span><span class="sxs-lookup"><span data-stu-id="cd333-131">EF Designer resources</span></span>
  - [<span data-ttu-id="cd333-132">Database First ワークフロー</span><span class="sxs-lookup"><span data-stu-id="cd333-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="cd333-133">Model First ワークフロー</span><span class="sxs-lookup"><span data-stu-id="cd333-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="cd333-134">列挙型のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="cd333-135">空間型のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="cd333-136">Table-Per Hierarchy 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="cd333-137">Table-Per Type 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="cd333-138">更新のためのストアド プロシージャ マッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="cd333-139">クエリのためのストアド プロシージャ マッピング</span><span class="sxs-lookup"><span data-stu-id="cd333-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="cd333-140">エンティティ分割</span><span class="sxs-lookup"><span data-stu-id="cd333-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="cd333-141">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="cd333-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="cd333-142">[定義クエリ](xref:ef6/modeling/designer/advanced/defining-query) (詳細)</span><span class="sxs-lookup"><span data-stu-id="cd333-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="cd333-143">[テーブル値関数](xref:ef6/modeling/designer/advanced/tvfs) (詳細)</span><span class="sxs-lookup"><span data-stu-id="cd333-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="cd333-144">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cd333-144">Other resources</span></span>
  - [<span data-ttu-id="cd333-145">非同期クエリと保存</span><span class="sxs-lookup"><span data-stu-id="cd333-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="cd333-146">WinForms とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="cd333-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="cd333-147">WPF とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="cd333-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="cd333-148">[自己追跡エンティティを使用した接続解除シナリオ](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (これは推奨されなくなりました)</span><span class="sxs-lookup"><span data-stu-id="cd333-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
