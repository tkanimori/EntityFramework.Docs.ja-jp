---
title: SQLite データベースプロバイダー-制限事項-EF Core
description: 他のプロバイダーと比較した場合の Entity Framework Core SQLite データベースプロバイダーの制限事項
author: bricelam
ms.date: 09/24/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 3d696474d401e8fd6c26a78067d292f0bb97a457
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062738"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="c4258-103">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-103">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="c4258-104">SQLite プロバイダーには、多くの移行に関する制限があります。</span><span class="sxs-lookup"><span data-stu-id="c4258-104">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="c4258-105">これらの制限事項のほとんどは、基になる SQLite データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="c4258-105">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="c4258-106">モデリングの制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-106">Modeling limitations</span></span>

<span data-ttu-id="c4258-107">一般的なリレーショナルライブラリ (Entity Framework リレーショナルデータベースプロバイダーによって共有されます) は、ほとんどのリレーショナルデータベースエンジンに共通のモデリング概念用の Api を定義します。</span><span class="sxs-lookup"><span data-stu-id="c4258-107">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="c4258-108">これらの概念のいくつかは、SQLite プロバイダーでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c4258-108">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="c4258-109">スキーマ</span><span class="sxs-lookup"><span data-stu-id="c4258-109">Schemas</span></span>
* <span data-ttu-id="c4258-110">シーケンス</span><span class="sxs-lookup"><span data-stu-id="c4258-110">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="c4258-111">クエリの制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-111">Query limitations</span></span>

<span data-ttu-id="c4258-112">SQLite では、次のデータ型はネイティブでサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c4258-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="c4258-113">これらの型の値の読み取りと書き込みを行うことができる EF Core、等値 () のクエリ `where e.Property == value` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c4258-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="c4258-114">ただし、比較や順序付けなどの操作では、クライアントでの評価が必要になります。</span><span class="sxs-lookup"><span data-stu-id="c4258-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="c4258-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="c4258-115">DateTimeOffset</span></span>
* <span data-ttu-id="c4258-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="c4258-116">Decimal</span></span>
* <span data-ttu-id="c4258-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="c4258-117">TimeSpan</span></span>
* <span data-ttu-id="c4258-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="c4258-118">UInt64</span></span>

<span data-ttu-id="c4258-119">で `DateTimeOffset` はなく、DateTime 値を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c4258-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="c4258-120">複数のタイムゾーンを処理する場合は、保存してから適切なタイムゾーンに変換する前に、値を UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c4258-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="c4258-121">この `Decimal` 型は、高レベルの精度を提供します。</span><span class="sxs-lookup"><span data-stu-id="c4258-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="c4258-122">ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c4258-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="c4258-123">[値コンバーター](xref:core/modeling/value-conversions)を使用して、クラスで10進数の使用を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="c4258-123">You can use a [value converter](xref:core/modeling/value-conversions) to continue using decimal in your classes.</span></span>

```csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="c4258-124">移行に関する制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-124">Migrations limitations</span></span>

<span data-ttu-id="c4258-125">SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c4258-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="c4258-126">サポートされていない操作の1つを SQLite データベースに適用しようとすると、が `NotSupportedException` スローされます。</span><span class="sxs-lookup"><span data-stu-id="c4258-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

<span data-ttu-id="c4258-127">特定の操作を実行するために再構築が試行されます。</span><span class="sxs-lookup"><span data-stu-id="c4258-127">A rebuild will be attempted in order to perform certain operations.</span></span> <span data-ttu-id="c4258-128">再構築を行うことができるのは、EF Core モデルの一部であるデータベースアーティファクトだけです。</span><span class="sxs-lookup"><span data-stu-id="c4258-128">Rebuilds are only possible for database artifacts that are part of your EF Core model.</span></span> <span data-ttu-id="c4258-129">データベースアーティファクトがモデルの一部ではない場合 (たとえば、移行中に手動で作成された場合)、 `NotSupportedException` がまだスローされます。</span><span class="sxs-lookup"><span data-stu-id="c4258-129">If a database artifact isn't part of the model--for example, if it was created manually inside a migration--then a `NotSupportedException` is still thrown.</span></span>

| <span data-ttu-id="c4258-130">操作</span><span class="sxs-lookup"><span data-stu-id="c4258-130">Operation</span></span>            | <span data-ttu-id="c4258-131">サポート対象</span><span class="sxs-lookup"><span data-stu-id="c4258-131">Supported?</span></span>  | <span data-ttu-id="c4258-132">バージョンが必要です</span><span class="sxs-lookup"><span data-stu-id="c4258-132">Requires version</span></span> |
|:---------------------|:------------|:-----------------|
| <span data-ttu-id="c4258-133">AddCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="c4258-133">AddCheckConstraint</span></span>   | <span data-ttu-id="c4258-134">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-134">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-135">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-135">5.0</span></span>              |
| <span data-ttu-id="c4258-136">Table.addcolumn</span><span class="sxs-lookup"><span data-stu-id="c4258-136">AddColumn</span></span>            | <span data-ttu-id="c4258-137">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-137">✔</span></span>           |                  |
| <span data-ttu-id="c4258-138">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="c4258-138">AddForeignKey</span></span>        | <span data-ttu-id="c4258-139">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-139">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-140">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-140">5.0</span></span>              |
| <span data-ttu-id="c4258-141">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="c4258-141">AddPrimaryKey</span></span>        | <span data-ttu-id="c4258-142">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-142">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-143">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-143">5.0</span></span>              |
| <span data-ttu-id="c4258-144">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="c4258-144">AddUniqueConstraint</span></span>  | <span data-ttu-id="c4258-145">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-145">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-146">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-146">5.0</span></span>              |
| <span data-ttu-id="c4258-147">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="c4258-147">AlterColumn</span></span>          | <span data-ttu-id="c4258-148">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-148">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-149">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-149">5.0</span></span>              |
| <span data-ttu-id="c4258-150">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="c4258-150">CreateIndex</span></span>          | <span data-ttu-id="c4258-151">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-151">✔</span></span>           |                  |
| <span data-ttu-id="c4258-152">CreateTable</span><span class="sxs-lookup"><span data-stu-id="c4258-152">CreateTable</span></span>          | <span data-ttu-id="c4258-153">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-153">✔</span></span>           |                  |
| <span data-ttu-id="c4258-154">DropCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="c4258-154">DropCheckConstraint</span></span>  | <span data-ttu-id="c4258-155">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-155">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-156">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-156">5.0</span></span>              |
| <span data-ttu-id="c4258-157">DropColumn</span><span class="sxs-lookup"><span data-stu-id="c4258-157">DropColumn</span></span>           | <span data-ttu-id="c4258-158">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-158">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-159">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-159">5.0</span></span>              |
| <span data-ttu-id="c4258-160">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="c4258-160">DropForeignKey</span></span>       | <span data-ttu-id="c4258-161">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-161">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-162">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-162">5.0</span></span>              |
| <span data-ttu-id="c4258-163">DropIndex</span><span class="sxs-lookup"><span data-stu-id="c4258-163">DropIndex</span></span>            | <span data-ttu-id="c4258-164">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-164">✔</span></span>           |                  |
| <span data-ttu-id="c4258-165">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="c4258-165">DropPrimaryKey</span></span>       | <span data-ttu-id="c4258-166">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-166">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-167">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-167">5.0</span></span>              |
| <span data-ttu-id="c4258-168">DropTable</span><span class="sxs-lookup"><span data-stu-id="c4258-168">DropTable</span></span>            | <span data-ttu-id="c4258-169">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-169">✔</span></span>           |                  |
| <span data-ttu-id="c4258-170">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="c4258-170">DropUniqueConstraint</span></span> | <span data-ttu-id="c4258-171">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-171">✔ (rebuild)</span></span> | <span data-ttu-id="c4258-172">5.0</span><span class="sxs-lookup"><span data-stu-id="c4258-172">5.0</span></span>              |
| <span data-ttu-id="c4258-173">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="c4258-173">RenameColumn</span></span>         | <span data-ttu-id="c4258-174">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-174">✔</span></span>           | <span data-ttu-id="c4258-175">2.2</span><span class="sxs-lookup"><span data-stu-id="c4258-175">2.2</span></span>              |
| <span data-ttu-id="c4258-176">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="c4258-176">RenameIndex</span></span>          | <span data-ttu-id="c4258-177">✔ (再構築)</span><span class="sxs-lookup"><span data-stu-id="c4258-177">✔ (rebuild)</span></span> |                  |
| <span data-ttu-id="c4258-178">RenameTable</span><span class="sxs-lookup"><span data-stu-id="c4258-178">RenameTable</span></span>          | <span data-ttu-id="c4258-179">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-179">✔</span></span>           |                  |
| <span data-ttu-id="c4258-180">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="c4258-180">EnsureSchema</span></span>         | <span data-ttu-id="c4258-181">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="c4258-181">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="c4258-182">DropSchema</span><span class="sxs-lookup"><span data-stu-id="c4258-182">DropSchema</span></span>           | <span data-ttu-id="c4258-183">✔ (非 op)</span><span class="sxs-lookup"><span data-stu-id="c4258-183">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="c4258-184">挿入</span><span class="sxs-lookup"><span data-stu-id="c4258-184">Insert</span></span>               | <span data-ttu-id="c4258-185">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-185">✔</span></span>           |                  |
| <span data-ttu-id="c4258-186">更新</span><span class="sxs-lookup"><span data-stu-id="c4258-186">Update</span></span>               | <span data-ttu-id="c4258-187">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-187">✔</span></span>           |                  |
| <span data-ttu-id="c4258-188">削除</span><span class="sxs-lookup"><span data-stu-id="c4258-188">Delete</span></span>               | <span data-ttu-id="c4258-189">✔</span><span class="sxs-lookup"><span data-stu-id="c4258-189">✔</span></span>           |                  |

### <a name="migrations-limitations-workaround"></a><span data-ttu-id="c4258-190">移行の制限の回避策</span><span class="sxs-lookup"><span data-stu-id="c4258-190">Migrations limitations workaround</span></span>

<span data-ttu-id="c4258-191">再構築を実行するために、移行でコードを手動で記述することで、これらの制限の一部を回避できます。</span><span class="sxs-lookup"><span data-stu-id="c4258-191">You can workaround some of these limitations by manually writing code in your migrations to perform a rebuild.</span></span> <span data-ttu-id="c4258-192">テーブルの再構築では、新しいテーブルを作成し、新しいテーブルにデータをコピーし、古いテーブルを削除して、新しいテーブルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="c4258-192">Table rebuilds involve creating a new table, copying data to the new table, dropping the old table, renaming the new table.</span></span> <span data-ttu-id="c4258-193">これらの手順の一部を実行するには、メソッドを使用する必要があり `Sql(string)` ます。</span><span class="sxs-lookup"><span data-stu-id="c4258-193">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="c4258-194">詳細については、SQLite のドキュメントで [他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter) 方法に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c4258-194">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

### <a name="idempotent-script-limitations"></a><span data-ttu-id="c4258-195">べき等スクリプトの制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-195">Idempotent script limitations</span></span>

<span data-ttu-id="c4258-196">他のデータベースとは異なり、SQLite には手続き型の言語が含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c4258-196">Unlike other databases, SQLite doesn't include a procedural language.</span></span> <span data-ttu-id="c4258-197">このため、べき等移行スクリプトに必要な if then ロジックを生成する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="c4258-197">Because of this, there is no way to generate the if-then logic required by the idempotent migration scripts.</span></span>

<span data-ttu-id="c4258-198">最後の移行がデータベースに適用されていることがわかっている場合は、その移行から最新の移行にスクリプトを生成できます。</span><span class="sxs-lookup"><span data-stu-id="c4258-198">If you know the last migration applied to a database, you can generate a script from that migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script CurrentMigration
```

<span data-ttu-id="c4258-199">それ以外の場合は、を使用して移行を適用することをお勧めし `dotnet ef database update` ます。</span><span class="sxs-lookup"><span data-stu-id="c4258-199">Otherwise, we recommend using `dotnet ef database update` to apply migrations.</span></span> <span data-ttu-id="c4258-200">EF Core 5.0 以降では、コマンドの実行時にデータベースファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c4258-200">Starting in EF Core 5.0, you can specify the database file when running the command.</span></span>

```dotnetcli
dotnet ef database update --connection "Data Source=My.db"
```

## <a name="see-also"></a><span data-ttu-id="c4258-201">関連項目</span><span class="sxs-lookup"><span data-stu-id="c4258-201">See also</span></span>

* [<span data-ttu-id="c4258-202">Microsoft データ Sqlite の非同期の制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-202">Microsoft.Data.Sqlite Async Limitations</span></span>](/dotnet/standard/data/sqlite/async)
* [<span data-ttu-id="c4258-203">ADO.NET の制限事項</span><span class="sxs-lookup"><span data-stu-id="c4258-203">Microsoft.Data.Sqlite ADO.NET Limitations</span></span>](/dotnet/standard/data/sqlite/adonet-limitations)
