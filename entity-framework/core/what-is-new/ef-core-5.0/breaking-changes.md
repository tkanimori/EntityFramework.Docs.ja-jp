---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618679"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="0dd2a-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="0dd2a-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="0dd2a-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="0dd2a-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="0dd2a-105">Summary</span></span>

| <span data-ttu-id="0dd2a-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="0dd2a-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="0dd2a-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="0dd2a-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="0dd2a-109">中間</span><span class="sxs-lookup"><span data-stu-id="0dd2a-109">Medium</span></span>     |
| [<span data-ttu-id="0dd2a-110">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="0dd2a-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="0dd2a-111">Low</span><span class="sxs-lookup"><span data-stu-id="0dd2a-111">Low</span></span>        |
| [<span data-ttu-id="0dd2a-112">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="0dd2a-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="0dd2a-113">低</span><span class="sxs-lookup"><span data-stu-id="0dd2a-113">Low</span></span>        |
| [<span data-ttu-id="0dd2a-114">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="0dd2a-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="0dd2a-115">低</span><span class="sxs-lookup"><span data-stu-id="0dd2a-115">Low</span></span>        |
| [<span data-ttu-id="0dd2a-116">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="0dd2a-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="0dd2a-117">Low</span><span class="sxs-lookup"><span data-stu-id="0dd2a-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="0dd2a-118">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="0dd2a-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="0dd2a-119">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="0dd2a-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="0dd2a-120">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-120">**Old behavior**</span></span>

<span data-ttu-id="0dd2a-121">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="0dd2a-122">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="0dd2a-123">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="0dd2a-124">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="0dd2a-125">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-125">**New behavior**</span></span>

<span data-ttu-id="0dd2a-126">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="0dd2a-127">これは、SpatiaLite の AddGeometryColumn 関数の基になる動作と厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="0dd2a-128">**理由**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-128">**Why**</span></span>

<span data-ttu-id="0dd2a-129">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="0dd2a-130">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-130">**Mitigations**</span></span>

<span data-ttu-id="0dd2a-131">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="0dd2a-132">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="0dd2a-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="0dd2a-133">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="0dd2a-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="0dd2a-134">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-134">**Old behavior**</span></span>

<span data-ttu-id="0dd2a-135">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="0dd2a-136">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="0dd2a-137">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-137">**New behavior**</span></span>

<span data-ttu-id="0dd2a-138">必須の依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="0dd2a-139">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="0dd2a-140">**理由**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-140">**Why**</span></span>

<span data-ttu-id="0dd2a-141">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="0dd2a-142">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-142">**Mitigations**</span></span>

<span data-ttu-id="0dd2a-143">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="0dd2a-144">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="0dd2a-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="0dd2a-145">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="0dd2a-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="0dd2a-146">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-146">**Old behavior**</span></span>

<span data-ttu-id="0dd2a-147">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="0dd2a-148">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-148">**New behavior**</span></span>

<span data-ttu-id="0dd2a-149">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="0dd2a-150">**理由**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-150">**Why**</span></span>

<span data-ttu-id="0dd2a-151">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="0dd2a-152">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-152">**Mitigations**</span></span>

<span data-ttu-id="0dd2a-153">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="0dd2a-154">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="0dd2a-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="0dd2a-155">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="0dd2a-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="0dd2a-156">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-156">**Old behavior**</span></span>

<span data-ttu-id="0dd2a-157">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="0dd2a-158">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-158">**New behavior**</span></span>

<span data-ttu-id="0dd2a-159">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="0dd2a-160">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="0dd2a-161">**理由**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-161">**Why**</span></span>

<span data-ttu-id="0dd2a-162">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="0dd2a-163">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="0dd2a-164">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-164">**Mitigations**</span></span>

<span data-ttu-id="0dd2a-165">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="0dd2a-166">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="0dd2a-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="0dd2a-167">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="0dd2a-167">Discriminators are read-only</span></span>

[<span data-ttu-id="0dd2a-168">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="0dd2a-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="0dd2a-169">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-169">**Old behavior**</span></span>

<span data-ttu-id="0dd2a-170">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="0dd2a-171">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-171">**New behavior**</span></span>

<span data-ttu-id="0dd2a-172">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="0dd2a-173">**理由**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-173">**Why**</span></span>

<span data-ttu-id="0dd2a-174">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="0dd2a-175">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="0dd2a-175">**Mitigations**</span></span>

<span data-ttu-id="0dd2a-176">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="0dd2a-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
