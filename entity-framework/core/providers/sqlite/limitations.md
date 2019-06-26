---
title: SQLite データベース プロバイダー - 制限事項 - EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: eaa7d5b1496172e4f3821433a1cd098ee7e8b737
ms.sourcegitcommit: 9bd64a1a71b7f7aeb044aeecc7c4785b57db1ec9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394799"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="bd44d-102">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="bd44d-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="bd44d-103">SQLite プロバイダーでは、いくつかの移行の制限があります。</span><span class="sxs-lookup"><span data-stu-id="bd44d-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="bd44d-104">これらの制限事項のほとんどは、基になる SQLite データベース エンジンの制限事項の結果し、EF に固有ではありません。</span><span class="sxs-lookup"><span data-stu-id="bd44d-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="bd44d-105">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="bd44d-105">Modeling limitations</span></span>

<span data-ttu-id="bd44d-106">(Entity Framework のリレーショナル データベース プロバイダーによって共有)、一般的なリレーショナル ライブラリは、モデリングの概念はほとんどのリレーショナル データベース エンジンに共通の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="bd44d-107">これらの概念のいくつかは、SQLite プロバイダーによってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="bd44d-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="bd44d-108">スキーマ</span><span class="sxs-lookup"><span data-stu-id="bd44d-108">Schemas</span></span>
* <span data-ttu-id="bd44d-109">シーケンス</span><span class="sxs-lookup"><span data-stu-id="bd44d-109">Sequences</span></span>
* <span data-ttu-id="bd44d-110">計算列</span><span class="sxs-lookup"><span data-stu-id="bd44d-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="bd44d-111">クエリの制限事項</span><span class="sxs-lookup"><span data-stu-id="bd44d-111">Query limitations</span></span>

<span data-ttu-id="bd44d-112">SQLite は、次のデータ型をネイティブにサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="bd44d-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="bd44d-113">EF Core が、これらの型と等しいかどうかのクエリの値を読み書きできます (`where e.Property == value`) もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bd44d-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also support.</span></span> <span data-ttu-id="bd44d-114">ただし、比較のようなその他の操作と、順序付けすると、クライアントで評価が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd44d-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="bd44d-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="bd44d-115">DateTimeOffset</span></span>
* <span data-ttu-id="bd44d-116">Decimal (10 進数型)</span><span class="sxs-lookup"><span data-stu-id="bd44d-116">Decimal</span></span>
* <span data-ttu-id="bd44d-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="bd44d-117">TimeSpan</span></span>
* <span data-ttu-id="bd44d-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="bd44d-118">UInt64</span></span>

<span data-ttu-id="bd44d-119">代わりに`DateTimeOffset`DateTime 値を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bd44d-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="bd44d-120">複数のタイム ゾーンを処理する際は、値を保存し、し、適切なタイム ゾーンに変換する前に UTC に変換するをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bd44d-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="bd44d-121">`Decimal`型には、精度の高いレベルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="bd44d-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="bd44d-122">有効桁数のレベルを必要としない場合をお勧め代わりに double を使用します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="bd44d-123">使用することができます、[値コンバーター](../../modeling/value-conversions.md)クラスでの 10 進数の使用を続行します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="bd44d-124">移行の制限事項</span><span class="sxs-lookup"><span data-stu-id="bd44d-124">Migrations limitations</span></span>

<span data-ttu-id="bd44d-125">SQLite データベース エンジンは、多数の他のリレーショナル データベースの大部分でサポートされているスキーマの操作をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="bd44d-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="bd44d-126">サポートされていない操作のいずれかの SQLite データベースに適用しようとしたかどうか、`NotSupportedException`がスローされます。</span><span class="sxs-lookup"><span data-stu-id="bd44d-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="bd44d-127">操作</span><span class="sxs-lookup"><span data-stu-id="bd44d-127">Operation</span></span>            | <span data-ttu-id="bd44d-128">サポートされていますか。</span><span class="sxs-lookup"><span data-stu-id="bd44d-128">Supported?</span></span> | <span data-ttu-id="bd44d-129">バージョンが必要です。</span><span class="sxs-lookup"><span data-stu-id="bd44d-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="bd44d-130">AddColumn</span><span class="sxs-lookup"><span data-stu-id="bd44d-130">AddColumn</span></span>            | <span data-ttu-id="bd44d-131">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-131">✔</span></span>          | <span data-ttu-id="bd44d-132">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-132">1.0</span></span>              |
| <span data-ttu-id="bd44d-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="bd44d-133">AddForeignKey</span></span>        | <span data-ttu-id="bd44d-134">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-134">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="bd44d-135">AddPrimaryKey</span></span>        | <span data-ttu-id="bd44d-136">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-136">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="bd44d-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="bd44d-138">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-138">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="bd44d-139">AlterColumn</span></span>          | <span data-ttu-id="bd44d-140">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-140">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="bd44d-141">CreateIndex</span></span>          | <span data-ttu-id="bd44d-142">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-142">✔</span></span>          | <span data-ttu-id="bd44d-143">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-143">1.0</span></span>              |
| <span data-ttu-id="bd44d-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="bd44d-144">CreateTable</span></span>          | <span data-ttu-id="bd44d-145">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-145">✔</span></span>          | <span data-ttu-id="bd44d-146">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-146">1.0</span></span>              |
| <span data-ttu-id="bd44d-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="bd44d-147">DropColumn</span></span>           | <span data-ttu-id="bd44d-148">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-148">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="bd44d-149">DropForeignKey</span></span>       | <span data-ttu-id="bd44d-150">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-150">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="bd44d-151">DropIndex</span></span>            | <span data-ttu-id="bd44d-152">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-152">✔</span></span>          | <span data-ttu-id="bd44d-153">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-153">1.0</span></span>              |
| <span data-ttu-id="bd44d-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="bd44d-154">DropPrimaryKey</span></span>       | <span data-ttu-id="bd44d-155">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-155">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="bd44d-156">DropTable</span></span>            | <span data-ttu-id="bd44d-157">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-157">✔</span></span>          | <span data-ttu-id="bd44d-158">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-158">1.0</span></span>              |
| <span data-ttu-id="bd44d-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="bd44d-159">DropUniqueConstraint</span></span> | <span data-ttu-id="bd44d-160">✗</span><span class="sxs-lookup"><span data-stu-id="bd44d-160">✗</span></span>          |                  |
| <span data-ttu-id="bd44d-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="bd44d-161">RenameColumn</span></span>         | <span data-ttu-id="bd44d-162">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-162">✔</span></span>          | <span data-ttu-id="bd44d-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="bd44d-163">2.2.2</span></span>            |
| <span data-ttu-id="bd44d-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="bd44d-164">RenameIndex</span></span>          | <span data-ttu-id="bd44d-165">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-165">✔</span></span>          | <span data-ttu-id="bd44d-166">2.1</span><span class="sxs-lookup"><span data-stu-id="bd44d-166">2.1</span></span>              |
| <span data-ttu-id="bd44d-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="bd44d-167">RenameTable</span></span>          | <span data-ttu-id="bd44d-168">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-168">✔</span></span>          | <span data-ttu-id="bd44d-169">1</span><span class="sxs-lookup"><span data-stu-id="bd44d-169">1.0</span></span>              |
| <span data-ttu-id="bd44d-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="bd44d-170">EnsureSchema</span></span>         | <span data-ttu-id="bd44d-171">✔ (操作なし)</span><span class="sxs-lookup"><span data-stu-id="bd44d-171">✔ (no-op)</span></span>  | <span data-ttu-id="bd44d-172">2.0</span><span class="sxs-lookup"><span data-stu-id="bd44d-172">2.0</span></span>              |
| <span data-ttu-id="bd44d-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="bd44d-173">DropSchema</span></span>           | <span data-ttu-id="bd44d-174">✔ (操作なし)</span><span class="sxs-lookup"><span data-stu-id="bd44d-174">✔ (no-op)</span></span>  | <span data-ttu-id="bd44d-175">2.0</span><span class="sxs-lookup"><span data-stu-id="bd44d-175">2.0</span></span>              |
| <span data-ttu-id="bd44d-176">挿入</span><span class="sxs-lookup"><span data-stu-id="bd44d-176">Insert</span></span>               | <span data-ttu-id="bd44d-177">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-177">✔</span></span>          | <span data-ttu-id="bd44d-178">2.0</span><span class="sxs-lookup"><span data-stu-id="bd44d-178">2.0</span></span>              |
| <span data-ttu-id="bd44d-179">更新</span><span class="sxs-lookup"><span data-stu-id="bd44d-179">Update</span></span>               | <span data-ttu-id="bd44d-180">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-180">✔</span></span>          | <span data-ttu-id="bd44d-181">2.0</span><span class="sxs-lookup"><span data-stu-id="bd44d-181">2.0</span></span>              |
| <span data-ttu-id="bd44d-182">削除</span><span class="sxs-lookup"><span data-stu-id="bd44d-182">Delete</span></span>               | <span data-ttu-id="bd44d-183">✔</span><span class="sxs-lookup"><span data-stu-id="bd44d-183">✔</span></span>          | <span data-ttu-id="bd44d-184">2.0</span><span class="sxs-lookup"><span data-stu-id="bd44d-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="bd44d-185">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="bd44d-185">Migrations limitations workaround</span></span>

<span data-ttu-id="bd44d-186">回避策をいくつかは、移行テーブルを実行するコードを手動で記述してこれらの制限の再構築します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="bd44d-187">テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd44d-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="bd44d-188">使用する必要があります、`Sql(string)`メソッドを次の手順の一部を実行します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="bd44d-189">参照してください[その他の種類のテーブル スキーマ変更を行う](http://sqlite.org/lang_altertable.html#otheralter)詳細については、SQLite ドキュメント。</span><span class="sxs-lookup"><span data-stu-id="bd44d-189">See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="bd44d-190">将来的に、EF がサポートこれらの操作の一部裏では、テーブルの再構築のアプローチを使用しています。</span><span class="sxs-lookup"><span data-stu-id="bd44d-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="bd44d-191">できます[当社の GitHub プロジェクトでこの機能を追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)します。</span><span class="sxs-lookup"><span data-stu-id="bd44d-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
