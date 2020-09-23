---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070797"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="94142-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="94142-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="94142-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94142-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="94142-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="94142-105">Summary</span></span>

| <span data-ttu-id="94142-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="94142-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="94142-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="94142-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="94142-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="94142-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="94142-109">中間</span><span class="sxs-lookup"><span data-stu-id="94142-109">Medium</span></span>     |
| [<span data-ttu-id="94142-110">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="94142-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="94142-111">中間</span><span class="sxs-lookup"><span data-stu-id="94142-111">Medium</span></span>     |
| [<span data-ttu-id="94142-112">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="94142-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="94142-113">Low</span><span class="sxs-lookup"><span data-stu-id="94142-113">Low</span></span>        |
| [<span data-ttu-id="94142-114">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="94142-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="94142-115">低</span><span class="sxs-lookup"><span data-stu-id="94142-115">Low</span></span>        |
| [<span data-ttu-id="94142-116">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="94142-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="94142-117">低</span><span class="sxs-lookup"><span data-stu-id="94142-117">Low</span></span>        |
| <span data-ttu-id="94142-118">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="94142-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="94142-119">低</span><span class="sxs-lookup"><span data-stu-id="94142-119">Low</span></span>        |
| [<span data-ttu-id="94142-120">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="94142-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="94142-121">低</span><span class="sxs-lookup"><span data-stu-id="94142-121">Low</span></span>        |
| [<span data-ttu-id="94142-122">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="94142-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="94142-123">低</span><span class="sxs-lookup"><span data-stu-id="94142-123">Low</span></span>        |
| [<span data-ttu-id="94142-124">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="94142-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="94142-125">低</span><span class="sxs-lookup"><span data-stu-id="94142-125">Low</span></span>        |
| [<span data-ttu-id="94142-126">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="94142-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="94142-127">Low</span><span class="sxs-lookup"><span data-stu-id="94142-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="94142-128">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="94142-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="94142-129">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="94142-130">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-130">**Old behavior**</span></span>

<span data-ttu-id="94142-131">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="94142-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="94142-132">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="94142-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="94142-133">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="94142-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="94142-134">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="94142-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="94142-135">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-135">**New behavior**</span></span>

<span data-ttu-id="94142-136">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94142-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="94142-137">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="94142-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="94142-138">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-138">**Why**</span></span>

<span data-ttu-id="94142-139">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="94142-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="94142-140">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-140">**Mitigations**</span></span>

<span data-ttu-id="94142-141">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="94142-141">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="94142-142">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="94142-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="94142-143">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="94142-144">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-144">**Old behavior**</span></span>

<span data-ttu-id="94142-145">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="94142-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="94142-146">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="94142-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="94142-147">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-147">**New behavior**</span></span>

<span data-ttu-id="94142-148">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="94142-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="94142-149">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="94142-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="94142-150">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-150">**Why**</span></span>

<span data-ttu-id="94142-151">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="94142-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="94142-152">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-152">**Mitigations**</span></span>

<span data-ttu-id="94142-153">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="94142-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="94142-154">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="94142-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="94142-155">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="94142-156">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-156">**Old behavior**</span></span>

<span data-ttu-id="94142-157">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="94142-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="94142-158">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-158">**New behavior**</span></span>

<span data-ttu-id="94142-159">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="94142-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="94142-160">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-160">**Why**</span></span>

<span data-ttu-id="94142-161">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="94142-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="94142-162">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-162">**Mitigations**</span></span>

<span data-ttu-id="94142-163">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="94142-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="94142-164">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="94142-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="94142-165">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="94142-166">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-166">**Old behavior**</span></span>

<span data-ttu-id="94142-167">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="94142-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="94142-168">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-168">**New behavior**</span></span>

<span data-ttu-id="94142-169">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="94142-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="94142-170">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-170">**Why**</span></span>

<span data-ttu-id="94142-171">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="94142-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="94142-172">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-172">**Mitigations**</span></span>

<span data-ttu-id="94142-173">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="94142-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="94142-174">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="94142-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="94142-175">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="94142-176">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-176">**Old behavior**</span></span>

<span data-ttu-id="94142-177">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="94142-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="94142-178">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-178">**New behavior**</span></span>

<span data-ttu-id="94142-179">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="94142-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="94142-180">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-180">**Why**</span></span>

<span data-ttu-id="94142-181">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="94142-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="94142-182">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-182">**Mitigations**</span></span>

<span data-ttu-id="94142-183">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="94142-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="94142-184">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="94142-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="94142-185">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="94142-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="94142-186">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="94142-187">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-187">**Old behavior**</span></span>

<span data-ttu-id="94142-188">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="94142-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="94142-189">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-189">**New behavior**</span></span>

<span data-ttu-id="94142-190">古い API は廃止され、新しいメソッド `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="94142-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="94142-191">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-191">**Why**</span></span>

<span data-ttu-id="94142-192">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="94142-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="94142-193">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-193">**Mitigations**</span></span>

<span data-ttu-id="94142-194">新しい API を使用するか、古い警告を一時的に中断します。</span><span class="sxs-lookup"><span data-stu-id="94142-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="94142-195">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="94142-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="94142-196">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="94142-197">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-197">**Old behavior**</span></span>

<span data-ttu-id="94142-198">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="94142-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="94142-199">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-199">**New behavior**</span></span>

<span data-ttu-id="94142-200">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="94142-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="94142-201">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-201">**Why**</span></span>

<span data-ttu-id="94142-202">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="94142-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="94142-203">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-203">**Mitigations**</span></span>

<span data-ttu-id="94142-204">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="94142-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="94142-205">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="94142-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="94142-206">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="94142-207">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-207">**Old behavior**</span></span>

<span data-ttu-id="94142-208">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="94142-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="94142-209">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-209">**New behavior**</span></span>

<span data-ttu-id="94142-210">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="94142-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="94142-211">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="94142-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="94142-212">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-212">**Why**</span></span>

<span data-ttu-id="94142-213">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="94142-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="94142-214">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="94142-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="94142-215">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-215">**Mitigations**</span></span>

<span data-ttu-id="94142-216">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="94142-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="94142-217">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="94142-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="94142-218">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="94142-218">Discriminators are read-only</span></span>

[<span data-ttu-id="94142-219">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="94142-220">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-220">**Old behavior**</span></span>

<span data-ttu-id="94142-221">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="94142-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="94142-222">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-222">**New behavior**</span></span>

<span data-ttu-id="94142-223">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="94142-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="94142-224">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-224">**Why**</span></span>

<span data-ttu-id="94142-225">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94142-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="94142-226">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-226">**Mitigations**</span></span>

<span data-ttu-id="94142-227">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="94142-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="94142-228">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="94142-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="94142-229">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="94142-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="94142-230">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="94142-230">**Old behavior**</span></span>

<span data-ttu-id="94142-231">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="94142-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="94142-232">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="94142-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="94142-233">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="94142-233">**New behavior**</span></span>

<span data-ttu-id="94142-234">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="94142-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="94142-235">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="94142-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="94142-236">**理由**</span><span class="sxs-lookup"><span data-stu-id="94142-236">**Why**</span></span>

<span data-ttu-id="94142-237">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="94142-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="94142-238">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="94142-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="94142-239">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="94142-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="94142-240">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="94142-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="94142-241">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="94142-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="94142-242">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="94142-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="94142-243">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="94142-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="94142-244">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="94142-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="94142-245">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94142-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="94142-246">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="94142-246">**Mitigations**</span></span>

<span data-ttu-id="94142-247">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="94142-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="94142-248">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="94142-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
