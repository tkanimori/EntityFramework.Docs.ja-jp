---
title: SQLite データベースプロバイダー-制限事項-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179767"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="0893a-102">SQLite EF Core データベースプロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="0893a-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="0893a-103">SQLite プロバイダーには、多くの移行に関する制限があります。</span><span class="sxs-lookup"><span data-stu-id="0893a-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="0893a-104">これらの制限事項のほとんどは、基になる SQLite データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="0893a-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="0893a-105">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="0893a-105">Modeling limitations</span></span>

<span data-ttu-id="0893a-106">一般的なリレーショナルライブラリ (Entity Framework リレーショナルデータベースプロバイダーによって共有されます) は、ほとんどのリレーショナルデータベースエンジンに共通のモデリング概念用の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="0893a-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="0893a-107">これらの概念のいくつかは、SQLite プロバイダーでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0893a-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="0893a-108">スキーマ</span><span class="sxs-lookup"><span data-stu-id="0893a-108">Schemas</span></span>
* <span data-ttu-id="0893a-109">シーケンス</span><span class="sxs-lookup"><span data-stu-id="0893a-109">Sequences</span></span>
* <span data-ttu-id="0893a-110">計算列</span><span class="sxs-lookup"><span data-stu-id="0893a-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="0893a-111">クエリの制限事項</span><span class="sxs-lookup"><span data-stu-id="0893a-111">Query limitations</span></span>

<span data-ttu-id="0893a-112">SQLite では、次のデータ型はネイティブでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0893a-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="0893a-113">EF Core は、これらの型の値の読み取りと書き込みが可能であり、等値 (`where e.Property == value`) のクエリもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0893a-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="0893a-114">ただし、比較や順序付けなどの操作では、クライアントでの評価が必要になります。</span><span class="sxs-lookup"><span data-stu-id="0893a-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="0893a-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="0893a-115">DateTimeOffset</span></span>
* <span data-ttu-id="0893a-116">10 進数</span><span class="sxs-lookup"><span data-stu-id="0893a-116">Decimal</span></span>
* <span data-ttu-id="0893a-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="0893a-117">TimeSpan</span></span>
* <span data-ttu-id="0893a-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="0893a-118">UInt64</span></span>

<span data-ttu-id="0893a-119">@No__t-0 の代わりに、DateTime 値を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0893a-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="0893a-120">複数のタイムゾーンを処理する場合は、保存してから適切なタイムゾーンに変換する前に、値を UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0893a-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="0893a-121">@No__t-0 型では、高レベルの精度が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0893a-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="0893a-122">ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0893a-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="0893a-123">[値コンバーター](../../modeling/value-conversions.md)を使用して、クラスで10進数の使用を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="0893a-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="0893a-124">移行に関する制限事項</span><span class="sxs-lookup"><span data-stu-id="0893a-124">Migrations limitations</span></span>

<span data-ttu-id="0893a-125">SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0893a-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="0893a-126">サポートされていない操作の1つを SQLite データベースに適用しようとすると、`NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0893a-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="0893a-127">操作</span><span class="sxs-lookup"><span data-stu-id="0893a-127">Operation</span></span>            | <span data-ttu-id="0893a-128">さ?</span><span class="sxs-lookup"><span data-stu-id="0893a-128">Supported?</span></span> | <span data-ttu-id="0893a-129">バージョンが必要です</span><span class="sxs-lookup"><span data-stu-id="0893a-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="0893a-130">Table.addcolumn</span><span class="sxs-lookup"><span data-stu-id="0893a-130">AddColumn</span></span>            | <span data-ttu-id="0893a-131">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-131">✔</span></span>          | <span data-ttu-id="0893a-132">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-132">1.0</span></span>              |
| <span data-ttu-id="0893a-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="0893a-133">AddForeignKey</span></span>        | <span data-ttu-id="0893a-134">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-134">✗</span></span>          |                  |
| <span data-ttu-id="0893a-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="0893a-135">AddPrimaryKey</span></span>        | <span data-ttu-id="0893a-136">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-136">✗</span></span>          |                  |
| <span data-ttu-id="0893a-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="0893a-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="0893a-138">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-138">✗</span></span>          |                  |
| <span data-ttu-id="0893a-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="0893a-139">AlterColumn</span></span>          | <span data-ttu-id="0893a-140">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-140">✗</span></span>          |                  |
| <span data-ttu-id="0893a-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="0893a-141">CreateIndex</span></span>          | <span data-ttu-id="0893a-142">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-142">✔</span></span>          | <span data-ttu-id="0893a-143">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-143">1.0</span></span>              |
| <span data-ttu-id="0893a-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="0893a-144">CreateTable</span></span>          | <span data-ttu-id="0893a-145">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-145">✔</span></span>          | <span data-ttu-id="0893a-146">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-146">1.0</span></span>              |
| <span data-ttu-id="0893a-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="0893a-147">DropColumn</span></span>           | <span data-ttu-id="0893a-148">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-148">✗</span></span>          |                  |
| <span data-ttu-id="0893a-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="0893a-149">DropForeignKey</span></span>       | <span data-ttu-id="0893a-150">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-150">✗</span></span>          |                  |
| <span data-ttu-id="0893a-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="0893a-151">DropIndex</span></span>            | <span data-ttu-id="0893a-152">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-152">✔</span></span>          | <span data-ttu-id="0893a-153">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-153">1.0</span></span>              |
| <span data-ttu-id="0893a-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="0893a-154">DropPrimaryKey</span></span>       | <span data-ttu-id="0893a-155">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-155">✗</span></span>          |                  |
| <span data-ttu-id="0893a-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="0893a-156">DropTable</span></span>            | <span data-ttu-id="0893a-157">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-157">✔</span></span>          | <span data-ttu-id="0893a-158">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-158">1.0</span></span>              |
| <span data-ttu-id="0893a-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="0893a-159">DropUniqueConstraint</span></span> | <span data-ttu-id="0893a-160">✗</span><span class="sxs-lookup"><span data-stu-id="0893a-160">✗</span></span>          |                  |
| <span data-ttu-id="0893a-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="0893a-161">RenameColumn</span></span>         | <span data-ttu-id="0893a-162">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-162">✔</span></span>          | <span data-ttu-id="0893a-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="0893a-163">2.2.2</span></span>            |
| <span data-ttu-id="0893a-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="0893a-164">RenameIndex</span></span>          | <span data-ttu-id="0893a-165">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-165">✔</span></span>          | <span data-ttu-id="0893a-166">2.1</span><span class="sxs-lookup"><span data-stu-id="0893a-166">2.1</span></span>              |
| <span data-ttu-id="0893a-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="0893a-167">RenameTable</span></span>          | <span data-ttu-id="0893a-168">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-168">✔</span></span>          | <span data-ttu-id="0893a-169">1.0</span><span class="sxs-lookup"><span data-stu-id="0893a-169">1.0</span></span>              |
| <span data-ttu-id="0893a-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="0893a-170">EnsureSchema</span></span>         | <span data-ttu-id="0893a-171">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="0893a-171">✔ (no-op)</span></span>  | <span data-ttu-id="0893a-172">2.0</span><span class="sxs-lookup"><span data-stu-id="0893a-172">2.0</span></span>              |
| <span data-ttu-id="0893a-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="0893a-173">DropSchema</span></span>           | <span data-ttu-id="0893a-174">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="0893a-174">✔ (no-op)</span></span>  | <span data-ttu-id="0893a-175">2.0</span><span class="sxs-lookup"><span data-stu-id="0893a-175">2.0</span></span>              |
| <span data-ttu-id="0893a-176">Insert</span><span class="sxs-lookup"><span data-stu-id="0893a-176">Insert</span></span>               | <span data-ttu-id="0893a-177">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-177">✔</span></span>          | <span data-ttu-id="0893a-178">2.0</span><span class="sxs-lookup"><span data-stu-id="0893a-178">2.0</span></span>              |
| <span data-ttu-id="0893a-179">更新</span><span class="sxs-lookup"><span data-stu-id="0893a-179">Update</span></span>               | <span data-ttu-id="0893a-180">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-180">✔</span></span>          | <span data-ttu-id="0893a-181">2.0</span><span class="sxs-lookup"><span data-stu-id="0893a-181">2.0</span></span>              |
| <span data-ttu-id="0893a-182">削除</span><span class="sxs-lookup"><span data-stu-id="0893a-182">Delete</span></span>               | <span data-ttu-id="0893a-183">✔</span><span class="sxs-lookup"><span data-stu-id="0893a-183">✔</span></span>          | <span data-ttu-id="0893a-184">2.0</span><span class="sxs-lookup"><span data-stu-id="0893a-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="0893a-185">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="0893a-185">Migrations limitations workaround</span></span>

<span data-ttu-id="0893a-186">これらの制限の一部を回避するには、移行でコードを手動で記述して、テーブルの再構築を実行します。</span><span class="sxs-lookup"><span data-stu-id="0893a-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="0893a-187">テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。</span><span class="sxs-lookup"><span data-stu-id="0893a-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="0893a-188">これらの手順の一部を実行するには、`Sql(string)` メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0893a-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="0893a-189">詳細については、SQLite のドキュメントで[他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter)方法に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0893a-189">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="0893a-190">今後、EF では、これらの操作の一部を、テーブルの再構築方法を使用してサポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="0893a-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="0893a-191">[この機能は、GitHub プロジェクトで追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)できます。</span><span class="sxs-lookup"><span data-stu-id="0893a-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
