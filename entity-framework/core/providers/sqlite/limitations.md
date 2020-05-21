---
title: SQLite データベースプロバイダー-制限事項-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672779"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="062be-102">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="062be-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="062be-103">SQLite プロバイダーには、多くの移行に関する制限があります。</span><span class="sxs-lookup"><span data-stu-id="062be-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="062be-104">これらの制限事項のほとんどは、基になる SQLite データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="062be-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="062be-105">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="062be-105">Modeling limitations</span></span>

<span data-ttu-id="062be-106">一般的なリレーショナルライブラリ (Entity Framework リレーショナルデータベースプロバイダーによって共有されます) は、ほとんどのリレーショナルデータベースエンジンに共通のモデリング概念用の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="062be-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="062be-107">これらの概念のいくつかは、SQLite プロバイダーでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="062be-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="062be-108">スキーマ</span><span class="sxs-lookup"><span data-stu-id="062be-108">Schemas</span></span>
* <span data-ttu-id="062be-109">シーケンス</span><span class="sxs-lookup"><span data-stu-id="062be-109">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="062be-110">クエリの制限事項</span><span class="sxs-lookup"><span data-stu-id="062be-110">Query limitations</span></span>

<span data-ttu-id="062be-111">SQLite では、次のデータ型はネイティブでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="062be-111">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="062be-112">これらの型の値の読み取りと書き込みを行うことができる EF Core、等値 () のクエリ `where e.Property == value` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="062be-112">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="062be-113">ただし、比較や順序付けなどの操作では、クライアントでの評価が必要になります。</span><span class="sxs-lookup"><span data-stu-id="062be-113">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="062be-114">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="062be-114">DateTimeOffset</span></span>
* <span data-ttu-id="062be-115">Decimal</span><span class="sxs-lookup"><span data-stu-id="062be-115">Decimal</span></span>
* <span data-ttu-id="062be-116">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="062be-116">TimeSpan</span></span>
* <span data-ttu-id="062be-117">UInt64</span><span class="sxs-lookup"><span data-stu-id="062be-117">UInt64</span></span>

<span data-ttu-id="062be-118">で `DateTimeOffset` はなく、DateTime 値を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="062be-118">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="062be-119">複数のタイムゾーンを処理する場合は、保存してから適切なタイムゾーンに変換する前に、値を UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="062be-119">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="062be-120">この `Decimal` 型は、高レベルの精度を提供します。</span><span class="sxs-lookup"><span data-stu-id="062be-120">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="062be-121">ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="062be-121">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="062be-122">[値コンバーター](../../modeling/value-conversions.md)を使用して、クラスで10進数の使用を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="062be-122">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="062be-123">移行に関する制限事項</span><span class="sxs-lookup"><span data-stu-id="062be-123">Migrations limitations</span></span>

<span data-ttu-id="062be-124">SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="062be-124">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="062be-125">サポートされていない操作の1つを SQLite データベースに適用しようとすると、が `NotSupportedException` スローされます。</span><span class="sxs-lookup"><span data-stu-id="062be-125">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="062be-126">Operation</span><span class="sxs-lookup"><span data-stu-id="062be-126">Operation</span></span>            | <span data-ttu-id="062be-127">サポート対象かどうか</span><span class="sxs-lookup"><span data-stu-id="062be-127">Supported?</span></span> | <span data-ttu-id="062be-128">バージョンが必要です</span><span class="sxs-lookup"><span data-stu-id="062be-128">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="062be-129">Table.addcolumn</span><span class="sxs-lookup"><span data-stu-id="062be-129">AddColumn</span></span>            | <span data-ttu-id="062be-130">✔</span><span class="sxs-lookup"><span data-stu-id="062be-130">✔</span></span>          | <span data-ttu-id="062be-131">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-131">1.0</span></span>              |
| <span data-ttu-id="062be-132">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="062be-132">AddForeignKey</span></span>        | <span data-ttu-id="062be-133">✗</span><span class="sxs-lookup"><span data-stu-id="062be-133">✗</span></span>          |                  |
| <span data-ttu-id="062be-134">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="062be-134">AddPrimaryKey</span></span>        | <span data-ttu-id="062be-135">✗</span><span class="sxs-lookup"><span data-stu-id="062be-135">✗</span></span>          |                  |
| <span data-ttu-id="062be-136">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="062be-136">AddUniqueConstraint</span></span>  | <span data-ttu-id="062be-137">✗</span><span class="sxs-lookup"><span data-stu-id="062be-137">✗</span></span>          |                  |
| <span data-ttu-id="062be-138">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="062be-138">AlterColumn</span></span>          | <span data-ttu-id="062be-139">✗</span><span class="sxs-lookup"><span data-stu-id="062be-139">✗</span></span>          |                  |
| <span data-ttu-id="062be-140">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="062be-140">CreateIndex</span></span>          | <span data-ttu-id="062be-141">✔</span><span class="sxs-lookup"><span data-stu-id="062be-141">✔</span></span>          | <span data-ttu-id="062be-142">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-142">1.0</span></span>              |
| <span data-ttu-id="062be-143">CreateTable</span><span class="sxs-lookup"><span data-stu-id="062be-143">CreateTable</span></span>          | <span data-ttu-id="062be-144">✔</span><span class="sxs-lookup"><span data-stu-id="062be-144">✔</span></span>          | <span data-ttu-id="062be-145">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-145">1.0</span></span>              |
| <span data-ttu-id="062be-146">DropColumn</span><span class="sxs-lookup"><span data-stu-id="062be-146">DropColumn</span></span>           | <span data-ttu-id="062be-147">✗</span><span class="sxs-lookup"><span data-stu-id="062be-147">✗</span></span>          |                  |
| <span data-ttu-id="062be-148">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="062be-148">DropForeignKey</span></span>       | <span data-ttu-id="062be-149">✗</span><span class="sxs-lookup"><span data-stu-id="062be-149">✗</span></span>          |                  |
| <span data-ttu-id="062be-150">DropIndex</span><span class="sxs-lookup"><span data-stu-id="062be-150">DropIndex</span></span>            | <span data-ttu-id="062be-151">✔</span><span class="sxs-lookup"><span data-stu-id="062be-151">✔</span></span>          | <span data-ttu-id="062be-152">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-152">1.0</span></span>              |
| <span data-ttu-id="062be-153">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="062be-153">DropPrimaryKey</span></span>       | <span data-ttu-id="062be-154">✗</span><span class="sxs-lookup"><span data-stu-id="062be-154">✗</span></span>          |                  |
| <span data-ttu-id="062be-155">DropTable</span><span class="sxs-lookup"><span data-stu-id="062be-155">DropTable</span></span>            | <span data-ttu-id="062be-156">✔</span><span class="sxs-lookup"><span data-stu-id="062be-156">✔</span></span>          | <span data-ttu-id="062be-157">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-157">1.0</span></span>              |
| <span data-ttu-id="062be-158">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="062be-158">DropUniqueConstraint</span></span> | <span data-ttu-id="062be-159">✗</span><span class="sxs-lookup"><span data-stu-id="062be-159">✗</span></span>          |                  |
| <span data-ttu-id="062be-160">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="062be-160">RenameColumn</span></span>         | <span data-ttu-id="062be-161">✔</span><span class="sxs-lookup"><span data-stu-id="062be-161">✔</span></span>          | <span data-ttu-id="062be-162">2.2.2</span><span class="sxs-lookup"><span data-stu-id="062be-162">2.2.2</span></span>            |
| <span data-ttu-id="062be-163">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="062be-163">RenameIndex</span></span>          | <span data-ttu-id="062be-164">✔</span><span class="sxs-lookup"><span data-stu-id="062be-164">✔</span></span>          | <span data-ttu-id="062be-165">2.1</span><span class="sxs-lookup"><span data-stu-id="062be-165">2.1</span></span>              |
| <span data-ttu-id="062be-166">RenameTable</span><span class="sxs-lookup"><span data-stu-id="062be-166">RenameTable</span></span>          | <span data-ttu-id="062be-167">✔</span><span class="sxs-lookup"><span data-stu-id="062be-167">✔</span></span>          | <span data-ttu-id="062be-168">1.0</span><span class="sxs-lookup"><span data-stu-id="062be-168">1.0</span></span>              |
| <span data-ttu-id="062be-169">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="062be-169">EnsureSchema</span></span>         | <span data-ttu-id="062be-170">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="062be-170">✔ (no-op)</span></span>  | <span data-ttu-id="062be-171">2.0</span><span class="sxs-lookup"><span data-stu-id="062be-171">2.0</span></span>              |
| <span data-ttu-id="062be-172">DropSchema</span><span class="sxs-lookup"><span data-stu-id="062be-172">DropSchema</span></span>           | <span data-ttu-id="062be-173">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="062be-173">✔ (no-op)</span></span>  | <span data-ttu-id="062be-174">2.0</span><span class="sxs-lookup"><span data-stu-id="062be-174">2.0</span></span>              |
| <span data-ttu-id="062be-175">挿入</span><span class="sxs-lookup"><span data-stu-id="062be-175">Insert</span></span>               | <span data-ttu-id="062be-176">✔</span><span class="sxs-lookup"><span data-stu-id="062be-176">✔</span></span>          | <span data-ttu-id="062be-177">2.0</span><span class="sxs-lookup"><span data-stu-id="062be-177">2.0</span></span>              |
| <span data-ttu-id="062be-178">更新</span><span class="sxs-lookup"><span data-stu-id="062be-178">Update</span></span>               | <span data-ttu-id="062be-179">✔</span><span class="sxs-lookup"><span data-stu-id="062be-179">✔</span></span>          | <span data-ttu-id="062be-180">2.0</span><span class="sxs-lookup"><span data-stu-id="062be-180">2.0</span></span>              |
| <span data-ttu-id="062be-181">削除</span><span class="sxs-lookup"><span data-stu-id="062be-181">Delete</span></span>               | <span data-ttu-id="062be-182">✔</span><span class="sxs-lookup"><span data-stu-id="062be-182">✔</span></span>          | <span data-ttu-id="062be-183">2.0</span><span class="sxs-lookup"><span data-stu-id="062be-183">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="062be-184">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="062be-184">Migrations limitations workaround</span></span>

<span data-ttu-id="062be-185">これらの制限の一部を回避するには、移行でコードを手動で記述して、テーブルの再構築を実行します。</span><span class="sxs-lookup"><span data-stu-id="062be-185">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="062be-186">テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。</span><span class="sxs-lookup"><span data-stu-id="062be-186">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="062be-187">これらの手順の一部を実行するには、メソッドを使用する必要があり `Sql(string)` ます。</span><span class="sxs-lookup"><span data-stu-id="062be-187">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="062be-188">詳細については、SQLite のドキュメントで[他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter)方法に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="062be-188">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="062be-189">今後、EF では、これらの操作の一部を、テーブルの再構築方法を使用してサポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="062be-189">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="062be-190">[この機能は、GitHub プロジェクトで追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)できます。</span><span class="sxs-lookup"><span data-stu-id="062be-190">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
