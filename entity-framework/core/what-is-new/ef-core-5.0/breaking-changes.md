---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065642"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="f711d-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="f711d-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="f711d-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="f711d-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="f711d-105">Summary</span></span>

| <span data-ttu-id="f711d-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="f711d-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="f711d-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="f711d-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="f711d-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="f711d-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="f711d-109">中間</span><span class="sxs-lookup"><span data-stu-id="f711d-109">Medium</span></span>     |
| [<span data-ttu-id="f711d-110">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="f711d-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="f711d-111">中間</span><span class="sxs-lookup"><span data-stu-id="f711d-111">Medium</span></span>     |
| [<span data-ttu-id="f711d-112">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="f711d-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="f711d-113">Low</span><span class="sxs-lookup"><span data-stu-id="f711d-113">Low</span></span>        |
| [<span data-ttu-id="f711d-114">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="f711d-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="f711d-115">低</span><span class="sxs-lookup"><span data-stu-id="f711d-115">Low</span></span>        |
| [<span data-ttu-id="f711d-116">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="f711d-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="f711d-117">低</span><span class="sxs-lookup"><span data-stu-id="f711d-117">Low</span></span>        |
| <span data-ttu-id="f711d-118">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="f711d-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="f711d-119">低</span><span class="sxs-lookup"><span data-stu-id="f711d-119">Low</span></span>        |
| [<span data-ttu-id="f711d-120">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="f711d-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="f711d-121">低</span><span class="sxs-lookup"><span data-stu-id="f711d-121">Low</span></span>        |
| [<span data-ttu-id="f711d-122">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="f711d-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="f711d-123">低</span><span class="sxs-lookup"><span data-stu-id="f711d-123">Low</span></span>        |
| [<span data-ttu-id="f711d-124">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="f711d-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="f711d-125">低</span><span class="sxs-lookup"><span data-stu-id="f711d-125">Low</span></span>        |
| [<span data-ttu-id="f711d-126">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="f711d-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="f711d-127">Low</span><span class="sxs-lookup"><span data-stu-id="f711d-127">Low</span></span>        |
| [<span data-ttu-id="f711d-128">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="f711d-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="f711d-129">Low</span><span class="sxs-lookup"><span data-stu-id="f711d-129">Low</span></span>        |
| [<span data-ttu-id="f711d-130">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="f711d-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="f711d-131">低</span><span class="sxs-lookup"><span data-stu-id="f711d-131">Low</span></span>        |
| [<span data-ttu-id="f711d-132">リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように</span><span class="sxs-lookup"><span data-stu-id="f711d-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="f711d-133">Low</span><span class="sxs-lookup"><span data-stu-id="f711d-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="f711d-134">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="f711d-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="f711d-135">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="f711d-136">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-136">**Old behavior**</span></span>

<span data-ttu-id="f711d-137">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="f711d-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="f711d-138">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="f711d-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="f711d-139">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="f711d-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="f711d-140">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f711d-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="f711d-141">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-141">**New behavior**</span></span>

<span data-ttu-id="f711d-142">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="f711d-143">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="f711d-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="f711d-144">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-144">**Why**</span></span>

<span data-ttu-id="f711d-145">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="f711d-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="f711d-146">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-146">**Mitigations**</span></span>

<span data-ttu-id="f711d-147">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="f711d-147">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="f711d-148">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="f711d-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="f711d-149">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="f711d-150">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-150">**Old behavior**</span></span>

<span data-ttu-id="f711d-151">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="f711d-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="f711d-152">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f711d-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="f711d-153">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-153">**New behavior**</span></span>

<span data-ttu-id="f711d-154">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="f711d-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="f711d-155">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="f711d-156">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-156">**Why**</span></span>

<span data-ttu-id="f711d-157">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="f711d-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="f711d-158">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-158">**Mitigations**</span></span>

<span data-ttu-id="f711d-159">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="f711d-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="f711d-160">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="f711d-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="f711d-161">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="f711d-162">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-162">**Old behavior**</span></span>

<span data-ttu-id="f711d-163">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="f711d-164">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-164">**New behavior**</span></span>

<span data-ttu-id="f711d-165">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="f711d-166">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-166">**Why**</span></span>

<span data-ttu-id="f711d-167">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="f711d-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="f711d-168">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-168">**Mitigations**</span></span>

<span data-ttu-id="f711d-169">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="f711d-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="f711d-170">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="f711d-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="f711d-171">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="f711d-172">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-172">**Old behavior**</span></span>

<span data-ttu-id="f711d-173">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="f711d-174">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-174">**New behavior**</span></span>

<span data-ttu-id="f711d-175">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="f711d-176">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-176">**Why**</span></span>

<span data-ttu-id="f711d-177">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="f711d-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="f711d-178">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-178">**Mitigations**</span></span>

<span data-ttu-id="f711d-179">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="f711d-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="f711d-180">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="f711d-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="f711d-181">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="f711d-182">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-182">**Old behavior**</span></span>

<span data-ttu-id="f711d-183">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="f711d-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="f711d-184">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-184">**New behavior**</span></span>

<span data-ttu-id="f711d-185">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="f711d-186">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-186">**Why**</span></span>

<span data-ttu-id="f711d-187">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="f711d-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="f711d-188">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-188">**Mitigations**</span></span>

<span data-ttu-id="f711d-189">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f711d-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="f711d-190">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/aspnet/EntityFrameworkCore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="f711d-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="f711d-191">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="f711d-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="f711d-192">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="f711d-193">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-193">**Old behavior**</span></span>

<span data-ttu-id="f711d-194">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="f711d-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="f711d-195">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-195">**New behavior**</span></span>

<span data-ttu-id="f711d-196">古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="f711d-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="f711d-197">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-197">**Why**</span></span>

<span data-ttu-id="f711d-198">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="f711d-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="f711d-199">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-199">**Mitigations**</span></span>

<span data-ttu-id="f711d-200">新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="f711d-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="f711d-201">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="f711d-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="f711d-202">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="f711d-203">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-203">**Old behavior**</span></span>

<span data-ttu-id="f711d-204">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="f711d-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="f711d-205">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-205">**New behavior**</span></span>

<span data-ttu-id="f711d-206">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="f711d-207">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-207">**Why**</span></span>

<span data-ttu-id="f711d-208">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="f711d-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="f711d-209">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-209">**Mitigations**</span></span>

<span data-ttu-id="f711d-210">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="f711d-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="f711d-211">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="f711d-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="f711d-212">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="f711d-213">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-213">**Old behavior**</span></span>

<span data-ttu-id="f711d-214">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="f711d-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="f711d-215">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-215">**New behavior**</span></span>

<span data-ttu-id="f711d-216">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="f711d-217">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f711d-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="f711d-218">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-218">**Why**</span></span>

<span data-ttu-id="f711d-219">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="f711d-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="f711d-220">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="f711d-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="f711d-221">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-221">**Mitigations**</span></span>

<span data-ttu-id="f711d-222">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="f711d-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
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

<span data-ttu-id="f711d-223">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="f711d-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="f711d-224">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="f711d-224">Discriminators are read-only</span></span>

[<span data-ttu-id="f711d-225">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="f711d-226">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-226">**Old behavior**</span></span>

<span data-ttu-id="f711d-227">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="f711d-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="f711d-228">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-228">**New behavior**</span></span>

<span data-ttu-id="f711d-229">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f711d-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="f711d-230">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-230">**Why**</span></span>

<span data-ttu-id="f711d-231">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="f711d-232">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-232">**Mitigations**</span></span>

<span data-ttu-id="f711d-233">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="f711d-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="f711d-234">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="f711d-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="f711d-235">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="f711d-236">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-236">**Old behavior**</span></span>

<span data-ttu-id="f711d-237">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="f711d-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="f711d-238">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="f711d-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="f711d-239">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-239">**New behavior**</span></span>

<span data-ttu-id="f711d-240">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="f711d-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="f711d-241">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="f711d-242">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-242">**Why**</span></span>

<span data-ttu-id="f711d-243">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="f711d-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="f711d-244">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="f711d-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="f711d-245">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="f711d-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="f711d-246">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="f711d-247">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="f711d-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="f711d-248">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="f711d-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="f711d-249">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="f711d-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="f711d-250">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="f711d-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="f711d-251">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f711d-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="f711d-252">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-252">**Mitigations**</span></span>

<span data-ttu-id="f711d-253">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="f711d-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="f711d-254">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="f711d-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="f711d-255">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="f711d-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="f711d-256">問題 #20294 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="f711d-257">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-257">**Old behavior**</span></span>

<span data-ttu-id="f711d-258">プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。</span><span class="sxs-lookup"><span data-stu-id="f711d-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="f711d-259">たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。</span><span class="sxs-lookup"><span data-stu-id="f711d-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="f711d-260">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-260">**New behavior**</span></span>

<span data-ttu-id="f711d-261">プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="f711d-262">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-262">**Why**</span></span>

<span data-ttu-id="f711d-263">プロバイダー固有のメソッドは、データベース関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="f711d-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="f711d-264">マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="f711d-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="f711d-265">クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="f711d-266">これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f711d-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="f711d-267">InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。</span><span class="sxs-lookup"><span data-stu-id="f711d-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="f711d-268">InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f711d-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="f711d-269">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-269">**Mitigations**</span></span>

<span data-ttu-id="f711d-270">データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="f711d-271">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="f711d-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="f711d-272">問題 #21089 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="f711d-273">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-273">**Old behavior**</span></span>

<span data-ttu-id="f711d-274">以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。</span><span class="sxs-lookup"><span data-stu-id="f711d-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="f711d-275">インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。</span><span class="sxs-lookup"><span data-stu-id="f711d-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="f711d-276">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-276">**New behavior**</span></span>

<span data-ttu-id="f711d-277">同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="f711d-278">これらのインデックスは、モデル内の名前で区別されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="f711d-279">慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="f711d-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="f711d-280">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-280">**Why**</span></span>

<span data-ttu-id="f711d-281">Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="f711d-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="f711d-282">この変更により、その方向に新たな一歩を踏み出すことができます。</span><span class="sxs-lookup"><span data-stu-id="f711d-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="f711d-283">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-283">**Mitigations**</span></span>

<span data-ttu-id="f711d-284">以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f711d-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="f711d-285">EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。</span><span class="sxs-lookup"><span data-stu-id="f711d-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="f711d-286">リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように</span><span class="sxs-lookup"><span data-stu-id="f711d-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="f711d-287">問題 #11160 の追跡</span><span class="sxs-lookup"><span data-stu-id="f711d-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="f711d-288">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-288">**Old behavior**</span></span>

<span data-ttu-id="f711d-289">以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="f711d-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="f711d-290">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="f711d-290">**New behavior**</span></span>

<span data-ttu-id="f711d-291">EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f711d-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="f711d-292">これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。</span><span class="sxs-lookup"><span data-stu-id="f711d-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="f711d-293">**理由**</span><span class="sxs-lookup"><span data-stu-id="f711d-293">**Why**</span></span>

<span data-ttu-id="f711d-294">コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。</span><span class="sxs-lookup"><span data-stu-id="f711d-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="f711d-295">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="f711d-295">**Mitigations**</span></span>

<span data-ttu-id="f711d-296">プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。</span><span class="sxs-lookup"><span data-stu-id="f711d-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
