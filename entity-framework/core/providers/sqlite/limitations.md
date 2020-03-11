---
title: SQLite データベースプロバイダー-制限事項-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414759"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="020d9-102">SQLite EF Core データベースプロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="020d9-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="020d9-103">SQLite プロバイダーには、多くの移行に関する制限があります。</span><span class="sxs-lookup"><span data-stu-id="020d9-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="020d9-104">これらの制限事項のほとんどは、基になる SQLite データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="020d9-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="020d9-105">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="020d9-105">Modeling limitations</span></span>

<span data-ttu-id="020d9-106">一般的なリレーショナルライブラリ (Entity Framework リレーショナルデータベースプロバイダーによって共有されます) は、ほとんどのリレーショナルデータベースエンジンに共通のモデリング概念用の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="020d9-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="020d9-107">これらの概念のいくつかは、SQLite プロバイダーでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="020d9-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="020d9-108">スキーマ</span><span class="sxs-lookup"><span data-stu-id="020d9-108">Schemas</span></span>
* <span data-ttu-id="020d9-109">シーケンス</span><span class="sxs-lookup"><span data-stu-id="020d9-109">Sequences</span></span>
* <span data-ttu-id="020d9-110">計算列</span><span class="sxs-lookup"><span data-stu-id="020d9-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="020d9-111">クエリの制限事項</span><span class="sxs-lookup"><span data-stu-id="020d9-111">Query limitations</span></span>

<span data-ttu-id="020d9-112">SQLite では、次のデータ型はネイティブでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="020d9-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="020d9-113">EF Core は、これらの型の値の読み取りと書き込みを行うことができ、等値 (`where e.Property == value`) のクエリもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="020d9-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="020d9-114">ただし、比較や順序付けなどの操作では、クライアントでの評価が必要になります。</span><span class="sxs-lookup"><span data-stu-id="020d9-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="020d9-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="020d9-115">DateTimeOffset</span></span>
* <span data-ttu-id="020d9-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="020d9-116">Decimal</span></span>
* <span data-ttu-id="020d9-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="020d9-117">TimeSpan</span></span>
* <span data-ttu-id="020d9-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="020d9-118">UInt64</span></span>

<span data-ttu-id="020d9-119">`DateTimeOffset`の代わりに、DateTime 値を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="020d9-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="020d9-120">複数のタイムゾーンを処理する場合は、保存してから適切なタイムゾーンに変換する前に、値を UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="020d9-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="020d9-121">`Decimal` 型は、高レベルの精度を提供します。</span><span class="sxs-lookup"><span data-stu-id="020d9-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="020d9-122">ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="020d9-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="020d9-123">[値コンバーター](../../modeling/value-conversions.md)を使用して、クラスで10進数の使用を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="020d9-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="020d9-124">移行に関する制限事項</span><span class="sxs-lookup"><span data-stu-id="020d9-124">Migrations limitations</span></span>

<span data-ttu-id="020d9-125">SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="020d9-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="020d9-126">サポートされていない操作の1つを SQLite データベースに適用しようとすると、`NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="020d9-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="020d9-127">操作</span><span class="sxs-lookup"><span data-stu-id="020d9-127">Operation</span></span>            | <span data-ttu-id="020d9-128">サポート対象かどうか</span><span class="sxs-lookup"><span data-stu-id="020d9-128">Supported?</span></span> | <span data-ttu-id="020d9-129">バージョンが必要です</span><span class="sxs-lookup"><span data-stu-id="020d9-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="020d9-130">Table.addcolumn</span><span class="sxs-lookup"><span data-stu-id="020d9-130">AddColumn</span></span>            | <span data-ttu-id="020d9-131">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-131">✔</span></span>          | <span data-ttu-id="020d9-132">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-132">1.0</span></span>              |
| <span data-ttu-id="020d9-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="020d9-133">AddForeignKey</span></span>        | <span data-ttu-id="020d9-134">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-134">✗</span></span>          |                  |
| <span data-ttu-id="020d9-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="020d9-135">AddPrimaryKey</span></span>        | <span data-ttu-id="020d9-136">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-136">✗</span></span>          |                  |
| <span data-ttu-id="020d9-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="020d9-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="020d9-138">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-138">✗</span></span>          |                  |
| <span data-ttu-id="020d9-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="020d9-139">AlterColumn</span></span>          | <span data-ttu-id="020d9-140">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-140">✗</span></span>          |                  |
| <span data-ttu-id="020d9-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="020d9-141">CreateIndex</span></span>          | <span data-ttu-id="020d9-142">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-142">✔</span></span>          | <span data-ttu-id="020d9-143">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-143">1.0</span></span>              |
| <span data-ttu-id="020d9-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="020d9-144">CreateTable</span></span>          | <span data-ttu-id="020d9-145">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-145">✔</span></span>          | <span data-ttu-id="020d9-146">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-146">1.0</span></span>              |
| <span data-ttu-id="020d9-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="020d9-147">DropColumn</span></span>           | <span data-ttu-id="020d9-148">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-148">✗</span></span>          |                  |
| <span data-ttu-id="020d9-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="020d9-149">DropForeignKey</span></span>       | <span data-ttu-id="020d9-150">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-150">✗</span></span>          |                  |
| <span data-ttu-id="020d9-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="020d9-151">DropIndex</span></span>            | <span data-ttu-id="020d9-152">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-152">✔</span></span>          | <span data-ttu-id="020d9-153">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-153">1.0</span></span>              |
| <span data-ttu-id="020d9-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="020d9-154">DropPrimaryKey</span></span>       | <span data-ttu-id="020d9-155">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-155">✗</span></span>          |                  |
| <span data-ttu-id="020d9-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="020d9-156">DropTable</span></span>            | <span data-ttu-id="020d9-157">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-157">✔</span></span>          | <span data-ttu-id="020d9-158">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-158">1.0</span></span>              |
| <span data-ttu-id="020d9-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="020d9-159">DropUniqueConstraint</span></span> | <span data-ttu-id="020d9-160">✗</span><span class="sxs-lookup"><span data-stu-id="020d9-160">✗</span></span>          |                  |
| <span data-ttu-id="020d9-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="020d9-161">RenameColumn</span></span>         | <span data-ttu-id="020d9-162">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-162">✔</span></span>          | <span data-ttu-id="020d9-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="020d9-163">2.2.2</span></span>            |
| <span data-ttu-id="020d9-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="020d9-164">RenameIndex</span></span>          | <span data-ttu-id="020d9-165">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-165">✔</span></span>          | <span data-ttu-id="020d9-166">2.1</span><span class="sxs-lookup"><span data-stu-id="020d9-166">2.1</span></span>              |
| <span data-ttu-id="020d9-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="020d9-167">RenameTable</span></span>          | <span data-ttu-id="020d9-168">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-168">✔</span></span>          | <span data-ttu-id="020d9-169">1.0</span><span class="sxs-lookup"><span data-stu-id="020d9-169">1.0</span></span>              |
| <span data-ttu-id="020d9-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="020d9-170">EnsureSchema</span></span>         | <span data-ttu-id="020d9-171">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="020d9-171">✔ (no-op)</span></span>  | <span data-ttu-id="020d9-172">2.0</span><span class="sxs-lookup"><span data-stu-id="020d9-172">2.0</span></span>              |
| <span data-ttu-id="020d9-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="020d9-173">DropSchema</span></span>           | <span data-ttu-id="020d9-174">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="020d9-174">✔ (no-op)</span></span>  | <span data-ttu-id="020d9-175">2.0</span><span class="sxs-lookup"><span data-stu-id="020d9-175">2.0</span></span>              |
| <span data-ttu-id="020d9-176">挿入</span><span class="sxs-lookup"><span data-stu-id="020d9-176">Insert</span></span>               | <span data-ttu-id="020d9-177">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-177">✔</span></span>          | <span data-ttu-id="020d9-178">2.0</span><span class="sxs-lookup"><span data-stu-id="020d9-178">2.0</span></span>              |
| <span data-ttu-id="020d9-179">更新</span><span class="sxs-lookup"><span data-stu-id="020d9-179">Update</span></span>               | <span data-ttu-id="020d9-180">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-180">✔</span></span>          | <span data-ttu-id="020d9-181">2.0</span><span class="sxs-lookup"><span data-stu-id="020d9-181">2.0</span></span>              |
| <span data-ttu-id="020d9-182">削除</span><span class="sxs-lookup"><span data-stu-id="020d9-182">Delete</span></span>               | <span data-ttu-id="020d9-183">✔</span><span class="sxs-lookup"><span data-stu-id="020d9-183">✔</span></span>          | <span data-ttu-id="020d9-184">2.0</span><span class="sxs-lookup"><span data-stu-id="020d9-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="020d9-185">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="020d9-185">Migrations limitations workaround</span></span>

<span data-ttu-id="020d9-186">これらの制限の一部を回避するには、移行でコードを手動で記述して、テーブルの再構築を実行します。</span><span class="sxs-lookup"><span data-stu-id="020d9-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="020d9-187">テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。</span><span class="sxs-lookup"><span data-stu-id="020d9-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="020d9-188">これらの手順の一部を実行するには、`Sql(string)` メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="020d9-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="020d9-189">詳細については、SQLite のドキュメントで[他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter)方法に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="020d9-189">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="020d9-190">今後、EF では、これらの操作の一部を、テーブルの再構築方法を使用してサポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="020d9-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="020d9-191">[この機能は、GitHub プロジェクトで追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)できます。</span><span class="sxs-lookup"><span data-stu-id="020d9-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
