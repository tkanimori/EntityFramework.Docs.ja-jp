---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210368"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="1ad50-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="1ad50-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="1ad50-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="1ad50-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="1ad50-105">Summary</span></span>

| <span data-ttu-id="1ad50-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="1ad50-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="1ad50-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="1ad50-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="1ad50-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="1ad50-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="1ad50-109">中間</span><span class="sxs-lookup"><span data-stu-id="1ad50-109">Medium</span></span>     |
| [<span data-ttu-id="1ad50-110">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="1ad50-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="1ad50-111">中間</span><span class="sxs-lookup"><span data-stu-id="1ad50-111">Medium</span></span>     |
| [<span data-ttu-id="1ad50-112">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="1ad50-113">Low</span><span class="sxs-lookup"><span data-stu-id="1ad50-113">Low</span></span>        |
| [<span data-ttu-id="1ad50-114">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="1ad50-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="1ad50-115">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-115">Low</span></span>        |
| [<span data-ttu-id="1ad50-116">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="1ad50-117">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-117">Low</span></span>        |
| <span data-ttu-id="1ad50-118">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="1ad50-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="1ad50-119">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-119">Low</span></span>        |
| [<span data-ttu-id="1ad50-120">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="1ad50-121">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-121">Low</span></span>        |
| [<span data-ttu-id="1ad50-122">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="1ad50-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="1ad50-123">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-123">Low</span></span>        |
| [<span data-ttu-id="1ad50-124">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="1ad50-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="1ad50-125">低</span><span class="sxs-lookup"><span data-stu-id="1ad50-125">Low</span></span>        |
| [<span data-ttu-id="1ad50-126">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="1ad50-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="1ad50-127">Low</span><span class="sxs-lookup"><span data-stu-id="1ad50-127">Low</span></span>        |
| [<span data-ttu-id="1ad50-128">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="1ad50-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="1ad50-129">Low</span><span class="sxs-lookup"><span data-stu-id="1ad50-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="1ad50-130">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="1ad50-131">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="1ad50-132">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-132">**Old behavior**</span></span>

<span data-ttu-id="1ad50-133">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="1ad50-134">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="1ad50-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="1ad50-135">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="1ad50-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="1ad50-136">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="1ad50-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="1ad50-137">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-137">**New behavior**</span></span>

<span data-ttu-id="1ad50-138">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="1ad50-139">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="1ad50-140">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-140">**Why**</span></span>

<span data-ttu-id="1ad50-141">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="1ad50-142">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-142">**Mitigations**</span></span>

<span data-ttu-id="1ad50-143">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-143">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="1ad50-144">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="1ad50-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="1ad50-145">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="1ad50-146">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-146">**Old behavior**</span></span>

<span data-ttu-id="1ad50-147">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="1ad50-148">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="1ad50-149">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-149">**New behavior**</span></span>

<span data-ttu-id="1ad50-150">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="1ad50-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="1ad50-151">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="1ad50-152">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-152">**Why**</span></span>

<span data-ttu-id="1ad50-153">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="1ad50-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="1ad50-154">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-154">**Mitigations**</span></span>

<span data-ttu-id="1ad50-155">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="1ad50-156">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="1ad50-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="1ad50-157">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="1ad50-158">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-158">**Old behavior**</span></span>

<span data-ttu-id="1ad50-159">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="1ad50-160">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-160">**New behavior**</span></span>

<span data-ttu-id="1ad50-161">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="1ad50-162">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-162">**Why**</span></span>

<span data-ttu-id="1ad50-163">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="1ad50-164">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-164">**Mitigations**</span></span>

<span data-ttu-id="1ad50-165">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="1ad50-166">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="1ad50-167">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="1ad50-168">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-168">**Old behavior**</span></span>

<span data-ttu-id="1ad50-169">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="1ad50-170">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-170">**New behavior**</span></span>

<span data-ttu-id="1ad50-171">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="1ad50-172">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-172">**Why**</span></span>

<span data-ttu-id="1ad50-173">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="1ad50-174">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-174">**Mitigations**</span></span>

<span data-ttu-id="1ad50-175">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="1ad50-176">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="1ad50-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="1ad50-177">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="1ad50-178">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-178">**Old behavior**</span></span>

<span data-ttu-id="1ad50-179">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="1ad50-180">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-180">**New behavior**</span></span>

<span data-ttu-id="1ad50-181">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="1ad50-182">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-182">**Why**</span></span>

<span data-ttu-id="1ad50-183">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="1ad50-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="1ad50-184">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-184">**Mitigations**</span></span>

<span data-ttu-id="1ad50-185">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1ad50-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="1ad50-186">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="1ad50-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="1ad50-187">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="1ad50-188">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="1ad50-189">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-189">**Old behavior**</span></span>

<span data-ttu-id="1ad50-190">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="1ad50-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="1ad50-191">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-191">**New behavior**</span></span>

<span data-ttu-id="1ad50-192">古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="1ad50-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="1ad50-193">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-193">**Why**</span></span>

<span data-ttu-id="1ad50-194">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="1ad50-195">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-195">**Mitigations**</span></span>

<span data-ttu-id="1ad50-196">新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="1ad50-197">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="1ad50-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="1ad50-198">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="1ad50-199">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-199">**Old behavior**</span></span>

<span data-ttu-id="1ad50-200">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="1ad50-201">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-201">**New behavior**</span></span>

<span data-ttu-id="1ad50-202">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="1ad50-203">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-203">**Why**</span></span>

<span data-ttu-id="1ad50-204">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="1ad50-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="1ad50-205">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-205">**Mitigations**</span></span>

<span data-ttu-id="1ad50-206">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="1ad50-207">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="1ad50-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="1ad50-208">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="1ad50-209">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-209">**Old behavior**</span></span>

<span data-ttu-id="1ad50-210">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="1ad50-211">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-211">**New behavior**</span></span>

<span data-ttu-id="1ad50-212">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="1ad50-213">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1ad50-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="1ad50-214">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-214">**Why**</span></span>

<span data-ttu-id="1ad50-215">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="1ad50-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="1ad50-216">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="1ad50-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="1ad50-217">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-217">**Mitigations**</span></span>

<span data-ttu-id="1ad50-218">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="1ad50-219">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="1ad50-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="1ad50-220">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="1ad50-220">Discriminators are read-only</span></span>

[<span data-ttu-id="1ad50-221">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="1ad50-222">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-222">**Old behavior**</span></span>

<span data-ttu-id="1ad50-223">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="1ad50-224">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-224">**New behavior**</span></span>

<span data-ttu-id="1ad50-225">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="1ad50-226">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-226">**Why**</span></span>

<span data-ttu-id="1ad50-227">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="1ad50-228">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-228">**Mitigations**</span></span>

<span data-ttu-id="1ad50-229">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="1ad50-230">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="1ad50-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="1ad50-231">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="1ad50-232">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-232">**Old behavior**</span></span>

<span data-ttu-id="1ad50-233">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="1ad50-234">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="1ad50-235">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-235">**New behavior**</span></span>

<span data-ttu-id="1ad50-236">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="1ad50-237">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="1ad50-238">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-238">**Why**</span></span>

<span data-ttu-id="1ad50-239">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="1ad50-240">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="1ad50-241">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="1ad50-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="1ad50-242">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="1ad50-243">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="1ad50-244">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="1ad50-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="1ad50-245">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="1ad50-246">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="1ad50-247">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1ad50-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="1ad50-248">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-248">**Mitigations**</span></span>

<span data-ttu-id="1ad50-249">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="1ad50-250">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="1ad50-251">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="1ad50-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="1ad50-252">問題 #20294 の追跡</span><span class="sxs-lookup"><span data-stu-id="1ad50-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="1ad50-253">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-253">**Old behavior**</span></span>

<span data-ttu-id="1ad50-254">プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="1ad50-255">たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。</span><span class="sxs-lookup"><span data-stu-id="1ad50-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="1ad50-256">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="1ad50-256">**New behavior**</span></span>

<span data-ttu-id="1ad50-257">プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="1ad50-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="1ad50-258">**理由**</span><span class="sxs-lookup"><span data-stu-id="1ad50-258">**Why**</span></span>

<span data-ttu-id="1ad50-259">プロバイダー固有のメソッドは、データベース関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="1ad50-260">マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="1ad50-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="1ad50-261">クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="1ad50-262">これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1ad50-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="1ad50-263">InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。</span><span class="sxs-lookup"><span data-stu-id="1ad50-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="1ad50-264">InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="1ad50-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="1ad50-265">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="1ad50-265">**Mitigations**</span></span>

<span data-ttu-id="1ad50-266">データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ad50-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
