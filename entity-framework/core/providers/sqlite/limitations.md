---
title: "SQLite データベース プロバイダーの制限 - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
ms.technology: entity-framework-core
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 08a4b8c26a3678491d412b333a7415cb45d4231f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="2f81c-102">SQLite EF コア データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="2f81c-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="2f81c-103">SQLite プロバイダーでは、いくつかの移行の制限があります。</span><span class="sxs-lookup"><span data-stu-id="2f81c-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="2f81c-104">これらの制限事項のほとんどは、基になる SQLite データベース エンジンの制限事項の結果、EF に限定されません。</span><span class="sxs-lookup"><span data-stu-id="2f81c-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="2f81c-105">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="2f81c-105">Modeling limitations</span></span>

<span data-ttu-id="2f81c-106">(Entity Framework プロバイダーによって共有リレーショナル データベース) 共通のリレーショナル ライブラリは、モデリングの概念は、ほとんどのリレーショナル データベース エンジンに共通の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="2f81c-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="2f81c-107">これらの概念のいくつかは、SQLite プロバイダーによってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2f81c-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="2f81c-108">スキーマ</span><span class="sxs-lookup"><span data-stu-id="2f81c-108">Schemas</span></span>
* <span data-ttu-id="2f81c-109">シーケンス</span><span class="sxs-lookup"><span data-stu-id="2f81c-109">Sequences</span></span>

## <a name="migrations-limitations"></a><span data-ttu-id="2f81c-110">移行の制限事項</span><span class="sxs-lookup"><span data-stu-id="2f81c-110">Migrations limitations</span></span>

<span data-ttu-id="2f81c-111">SQLite データベース エンジンは、多数の他のリレーショナル データベースの過半数によってサポートされているスキーマの操作をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="2f81c-111">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="2f81c-112">SQLite データベースにサポートされていない操作の 1 つを適用しようとするかどうか、`NotSupportedException`がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2f81c-112">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="2f81c-113">操作</span><span class="sxs-lookup"><span data-stu-id="2f81c-113">Operation</span></span>            | <span data-ttu-id="2f81c-114">サポートされているか。</span><span class="sxs-lookup"><span data-stu-id="2f81c-114">Supported?</span></span> |
| -------------------- | ---------- |
| <span data-ttu-id="2f81c-115">AddColumn</span><span class="sxs-lookup"><span data-stu-id="2f81c-115">AddColumn</span></span>            | <span data-ttu-id="2f81c-116">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-116">✔</span></span>          |
| <span data-ttu-id="2f81c-117">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="2f81c-117">AddForeignKey</span></span>        | <span data-ttu-id="2f81c-118">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-118">✗</span></span>          |
| <span data-ttu-id="2f81c-119">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="2f81c-119">AddPrimaryKey</span></span>        | <span data-ttu-id="2f81c-120">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-120">✗</span></span>          |
| <span data-ttu-id="2f81c-121">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="2f81c-121">AddUniqueConstraint</span></span>  | <span data-ttu-id="2f81c-122">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-122">✗</span></span>          |
| <span data-ttu-id="2f81c-123">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="2f81c-123">AlterColumn</span></span>          | <span data-ttu-id="2f81c-124">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-124">✗</span></span>          |
| <span data-ttu-id="2f81c-125">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="2f81c-125">CreateIndex</span></span>          | <span data-ttu-id="2f81c-126">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-126">✔</span></span>          |
| <span data-ttu-id="2f81c-127">CreateTable</span><span class="sxs-lookup"><span data-stu-id="2f81c-127">CreateTable</span></span>          | <span data-ttu-id="2f81c-128">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-128">✔</span></span>          |
| <span data-ttu-id="2f81c-129">DropColumn</span><span class="sxs-lookup"><span data-stu-id="2f81c-129">DropColumn</span></span>           | <span data-ttu-id="2f81c-130">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-130">✗</span></span>          |
| <span data-ttu-id="2f81c-131">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="2f81c-131">DropForeignKey</span></span>       | <span data-ttu-id="2f81c-132">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-132">✗</span></span>          |
| <span data-ttu-id="2f81c-133">DropIndex</span><span class="sxs-lookup"><span data-stu-id="2f81c-133">DropIndex</span></span>            | <span data-ttu-id="2f81c-134">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-134">✔</span></span>          |
| <span data-ttu-id="2f81c-135">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="2f81c-135">DropPrimaryKey</span></span>       | <span data-ttu-id="2f81c-136">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-136">✗</span></span>          |
| <span data-ttu-id="2f81c-137">DropTable</span><span class="sxs-lookup"><span data-stu-id="2f81c-137">DropTable</span></span>            | <span data-ttu-id="2f81c-138">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-138">✔</span></span>          |
| <span data-ttu-id="2f81c-139">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="2f81c-139">DropUniqueConstraint</span></span> | <span data-ttu-id="2f81c-140">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-140">✗</span></span>          |
| <span data-ttu-id="2f81c-141">Renamecolumn です。</span><span class="sxs-lookup"><span data-stu-id="2f81c-141">RenameColumn</span></span>         | <span data-ttu-id="2f81c-142">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-142">✗</span></span>          |
| <span data-ttu-id="2f81c-143">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="2f81c-143">RenameIndex</span></span>          | <span data-ttu-id="2f81c-144">✗</span><span class="sxs-lookup"><span data-stu-id="2f81c-144">✗</span></span>          |
| <span data-ttu-id="2f81c-145">RenameTable</span><span class="sxs-lookup"><span data-stu-id="2f81c-145">RenameTable</span></span>          | <span data-ttu-id="2f81c-146">✔</span><span class="sxs-lookup"><span data-stu-id="2f81c-146">✔</span></span>          |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="2f81c-147">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="2f81c-147">Migrations limitations workaround</span></span>

<span data-ttu-id="2f81c-148">回避策をいくつかを手動でテーブルを実行する、移行でコードを記述してこれらの制限を再構築します。</span><span class="sxs-lookup"><span data-stu-id="2f81c-148">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="2f81c-149">テーブルを再構築では、既存のテーブルの名前を変更する、新しいテーブルを作成する、新しいテーブルにデータをコピーおよび古いテーブルを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f81c-149">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="2f81c-150">使用する必要があります、`Sql(string)`メソッドを次の手順の一部を実行します。</span><span class="sxs-lookup"><span data-stu-id="2f81c-150">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="2f81c-151">参照してください[その他の種類のテーブルのスキーマ変更を行う](http://sqlite.org/lang_altertable.html#otheralter)詳細については、SQLite ドキュメント。</span><span class="sxs-lookup"><span data-stu-id="2f81c-151">See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="2f81c-152">将来的に、EF 可能性がありますサポートこれらの操作のいくつかのアプローチを使用して、テーブルの再構築は背後でします。</span><span class="sxs-lookup"><span data-stu-id="2f81c-152">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="2f81c-153">実行できます[当社の GitHub プロジェクトでこの機能を追跡](https://github.com/aspnet/EntityFramework/issues/329)です。</span><span class="sxs-lookup"><span data-stu-id="2f81c-153">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFramework/issues/329).</span></span>
