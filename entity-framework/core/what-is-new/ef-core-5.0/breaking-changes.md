---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 7a13c9a6f6bd299991c379ec490480e1fbb4ba46
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635472"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="2642a-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="2642a-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="2642a-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="2642a-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="2642a-105">Summary</span></span>

| <span data-ttu-id="2642a-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="2642a-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="2642a-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="2642a-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="2642a-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="2642a-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="2642a-109">中間</span><span class="sxs-lookup"><span data-stu-id="2642a-109">Medium</span></span>     |
| [<span data-ttu-id="2642a-110">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="2642a-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="2642a-111">中間</span><span class="sxs-lookup"><span data-stu-id="2642a-111">Medium</span></span>     |
| [<span data-ttu-id="2642a-112">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="2642a-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="2642a-113">中間</span><span class="sxs-lookup"><span data-stu-id="2642a-113">Medium</span></span>     |
| [<span data-ttu-id="2642a-114">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="2642a-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="2642a-115">低</span><span class="sxs-lookup"><span data-stu-id="2642a-115">Low</span></span>        |
| [<span data-ttu-id="2642a-116">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="2642a-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="2642a-117">低</span><span class="sxs-lookup"><span data-stu-id="2642a-117">Low</span></span>        |
| [<span data-ttu-id="2642a-118">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="2642a-119">低</span><span class="sxs-lookup"><span data-stu-id="2642a-119">Low</span></span>        |
| <span data-ttu-id="2642a-120">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="2642a-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="2642a-121">低</span><span class="sxs-lookup"><span data-stu-id="2642a-121">Low</span></span>        |
| [<span data-ttu-id="2642a-122">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="2642a-123">低</span><span class="sxs-lookup"><span data-stu-id="2642a-123">Low</span></span>        |
| [<span data-ttu-id="2642a-124">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="2642a-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="2642a-125">低</span><span class="sxs-lookup"><span data-stu-id="2642a-125">Low</span></span>        |
| [<span data-ttu-id="2642a-126">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="2642a-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="2642a-127">低</span><span class="sxs-lookup"><span data-stu-id="2642a-127">Low</span></span>        |
| [<span data-ttu-id="2642a-128">移行による ToView() の処理方法が変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-128">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="2642a-129">低</span><span class="sxs-lookup"><span data-stu-id="2642a-129">Low</span></span>        |
| [<span data-ttu-id="2642a-130">ToTable(null) により、エンティティ型はテーブルにマップされていないものとしてマークされます</span><span class="sxs-lookup"><span data-stu-id="2642a-130">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="2642a-131">低</span><span class="sxs-lookup"><span data-stu-id="2642a-131">Low</span></span>        |
| [<span data-ttu-id="2642a-132">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="2642a-132">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="2642a-133">Low</span><span class="sxs-lookup"><span data-stu-id="2642a-133">Low</span></span>        |
| [<span data-ttu-id="2642a-134">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="2642a-134">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="2642a-135">低</span><span class="sxs-lookup"><span data-stu-id="2642a-135">Low</span></span>        |
| [<span data-ttu-id="2642a-136">IProperty.GetColumnName() は古い形式になりました</span><span class="sxs-lookup"><span data-stu-id="2642a-136">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="2642a-137">低</span><span class="sxs-lookup"><span data-stu-id="2642a-137">Low</span></span>        |
| [<span data-ttu-id="2642a-138">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="2642a-138">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="2642a-139">低</span><span class="sxs-lookup"><span data-stu-id="2642a-139">Low</span></span>        |
| [<span data-ttu-id="2642a-140">リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています</span><span class="sxs-lookup"><span data-stu-id="2642a-140">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="2642a-141">Low</span><span class="sxs-lookup"><span data-stu-id="2642a-141">Low</span></span>        |
| [<span data-ttu-id="2642a-142">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="2642a-142">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="2642a-143">低</span><span class="sxs-lookup"><span data-stu-id="2642a-143">Low</span></span>        |
| [<span data-ttu-id="2642a-144">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="2642a-144">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="2642a-145">低</span><span class="sxs-lookup"><span data-stu-id="2642a-145">Low</span></span>        |
| [<span data-ttu-id="2642a-146">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="2642a-146">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="2642a-147">低</span><span class="sxs-lookup"><span data-stu-id="2642a-147">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="2642a-148">影響が中程度の変更</span><span class="sxs-lookup"><span data-stu-id="2642a-148">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="2642a-149">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="2642a-149">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="2642a-150">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-150">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-151">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-151">Old behavior</span></span>

<span data-ttu-id="2642a-152">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2642a-152">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="2642a-153">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-153">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-154">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-154">New behavior</span></span>

<span data-ttu-id="2642a-155">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="2642a-155">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="2642a-156">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-156">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="2642a-157">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-157">Why</span></span>

<span data-ttu-id="2642a-158">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="2642a-158">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-159">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-159">Mitigations</span></span>

<span data-ttu-id="2642a-160">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="2642a-160">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="2642a-161">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="2642a-161">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="2642a-162">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-162">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-163">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-163">Old behavior</span></span>

<span data-ttu-id="2642a-164">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="2642a-164">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="2642a-165">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="2642a-165">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-166">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-166">New behavior</span></span>

<span data-ttu-id="2642a-167">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-167">APIs for defining query are deprecated.</span></span> <span data-ttu-id="2642a-168">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-168">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-169">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-169">Why</span></span>

<span data-ttu-id="2642a-170">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="2642a-170">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="2642a-171">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="2642a-171">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="2642a-172">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="2642a-172">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="2642a-173">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-173">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="2642a-174">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="2642a-174">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="2642a-175">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="2642a-175">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="2642a-176">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="2642a-176">So we decided to simplify the concept.</span></span> <span data-ttu-id="2642a-177">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="2642a-177">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="2642a-178">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2642a-178">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-179">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-179">Mitigations</span></span>

<span data-ttu-id="2642a-180">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="2642a-180">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="2642a-181">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="2642a-181">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="2642a-182">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="2642a-182">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="2642a-183">イシュー #2693 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-183">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-184">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-184">Old behavior</span></span>

<span data-ttu-id="2642a-185">EF Core 3.1 では、非 null 値に意図的に初期化された参照ナビゲーションが、キー値が一致するかどうかに関係なく、データベースからのエンティティ インスタンスによって上書きされることがあります。</span><span class="sxs-lookup"><span data-stu-id="2642a-185">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="2642a-186">ただし、それ以外の場合は逆になり、EF Core 3.1 で既存の非 null 値のままになります。</span><span class="sxs-lookup"><span data-stu-id="2642a-186">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-187">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-187">New behavior</span></span>

<span data-ttu-id="2642a-188">EF Core 5.0 以降では、非 null 参照ナビゲーションは、クエリから返されたインスタンスによって上書きされることはありません。</span><span class="sxs-lookup"><span data-stu-id="2642a-188">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="2642a-189">"_コレクション_" ナビゲーションの空のコレクションへの意図的な初期化は、引き続きサポートされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="2642a-189">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-190">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-190">Why</span></span>

<span data-ttu-id="2642a-191">参照ナビゲーション プロパティを "空の" エンティティ インスタンスに初期化すると、あいまいな状態になります。</span><span class="sxs-lookup"><span data-stu-id="2642a-191">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="2642a-192">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2642a-192">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="2642a-193">通常、ブログと作成者のクエリにおいては、最初に `Blog` インスタンスを作成した後、データベースから返されたデータに基づいて適切な `Author` インスタンスを設定します。</span><span class="sxs-lookup"><span data-stu-id="2642a-193">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="2642a-194">ただし、この場合、`Blog.Author` のすべてのプロパティは、空の `Author` に既に初期化されています。</span><span class="sxs-lookup"><span data-stu-id="2642a-194">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="2642a-195">ただし、EF Core には、このインスタンスが "空" であることを認識する方法がありません。</span><span class="sxs-lookup"><span data-stu-id="2642a-195">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="2642a-196">そのため、このインスタンスを上書きすると、有効な `Author` が暗黙的に破棄される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-196">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="2642a-197">したがって、EF Core 5.0 では、既に初期化されているナビゲーションは常に上書きされないようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-197">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="2642a-198">この新しい動作は、EF6 の動作ともほとんどの場合は一致しますが、調査において EF6 と一致しない場合がいくつか見つかっています。</span><span class="sxs-lookup"><span data-stu-id="2642a-198">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="2642a-199">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-199">Mitigations</span></span>

<span data-ttu-id="2642a-200">この断絶が発生した場合の修正方法は、参照ナビゲーション プロパティの意図的な初期化を止めることです。</span><span class="sxs-lookup"><span data-stu-id="2642a-200">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="2642a-201">影響が少ない変更</span><span class="sxs-lookup"><span data-stu-id="2642a-201">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="2642a-202">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="2642a-202">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="2642a-203">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-203">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-204">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-204">Old behavior</span></span>

<span data-ttu-id="2642a-205">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-205">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="2642a-206">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="2642a-206">However, it only ever affected database creation.</span></span> <span data-ttu-id="2642a-207">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="2642a-207">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="2642a-208">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/dotnet/efcore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="2642a-208">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-209">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-209">New behavior</span></span>

<span data-ttu-id="2642a-210">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-210">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="2642a-211">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="2642a-211">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-212">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-212">Why</span></span>

<span data-ttu-id="2642a-213">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="2642a-213">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-214">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-214">Mitigations</span></span>

<span data-ttu-id="2642a-215">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="2642a-215">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="2642a-216">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="2642a-216">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="2642a-217">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-217">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-218">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-218">Old behavior</span></span>

<span data-ttu-id="2642a-219">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-219">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-220">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-220">New behavior</span></span>

<span data-ttu-id="2642a-221">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-221">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-222">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-222">Why</span></span>

<span data-ttu-id="2642a-223">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="2642a-223">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-224">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-224">Mitigations</span></span>

<span data-ttu-id="2642a-225">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="2642a-225">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="2642a-226">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-226">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="2642a-227">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-227">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-228">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-228">Old behavior</span></span>

<span data-ttu-id="2642a-229">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-229">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-230">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-230">New behavior</span></span>

<span data-ttu-id="2642a-231">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-231">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-232">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-232">Why</span></span>

<span data-ttu-id="2642a-233">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="2642a-233">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-234">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-234">Mitigations</span></span>

<span data-ttu-id="2642a-235">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="2642a-235">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="2642a-236">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="2642a-236">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="2642a-237">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-237">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-238">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-238">Old behavior</span></span>

<span data-ttu-id="2642a-239">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-239">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-240">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-240">New behavior</span></span>

<span data-ttu-id="2642a-241">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-241">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-242">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-242">Why</span></span>

<span data-ttu-id="2642a-243">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="2642a-243">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-244">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-244">Mitigations</span></span>

<span data-ttu-id="2642a-245">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2642a-245">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="2642a-246">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/dotnet/efcore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="2642a-246">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="2642a-247">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-247">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="2642a-248">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-248">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-249">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-249">Old behavior</span></span>

<span data-ttu-id="2642a-250">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="2642a-250">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-251">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-251">New behavior</span></span>

<span data-ttu-id="2642a-252">古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="2642a-252">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-253">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-253">Why</span></span>

<span data-ttu-id="2642a-254">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-254">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-255">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-255">Mitigations</span></span>

<span data-ttu-id="2642a-256">新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="2642a-256">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="2642a-257">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="2642a-257">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="2642a-258">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-258">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-259">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-259">Old behavior</span></span>

<span data-ttu-id="2642a-260">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-260">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-261">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-261">New behavior</span></span>

<span data-ttu-id="2642a-262">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-262">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-263">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-263">Why</span></span>

<span data-ttu-id="2642a-264">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="2642a-264">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-265">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-265">Mitigations</span></span>

<span data-ttu-id="2642a-266">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="2642a-266">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="2642a-267">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="2642a-267">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="2642a-268">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-268">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-269">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-269">Old behavior</span></span>

<span data-ttu-id="2642a-270">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-270">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-271">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-271">New behavior</span></span>

<span data-ttu-id="2642a-272">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-272">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="2642a-273">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2642a-273">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-274">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-274">Why</span></span>

<span data-ttu-id="2642a-275">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="2642a-275">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="2642a-276">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="2642a-276">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-277">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-277">Mitigations</span></span>

<span data-ttu-id="2642a-278">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="2642a-278">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="2642a-279">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="2642a-279">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="2642a-280">移行による ToView() の処理方法が変更されました</span><span class="sxs-lookup"><span data-stu-id="2642a-280">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="2642a-281">問題 #2725 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-281">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-282">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-282">Old behavior</span></span>

<span data-ttu-id="2642a-283">`ToView(string)` を呼び出すと、エンティティ型がビューにマップされるだけでなく、移行時に無視されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-283">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-284">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-284">New behavior</span></span>

<span data-ttu-id="2642a-285">`ToView(string)` により、エンティティ型はビューにマップされるだけでなく、テーブルにマップされていないものとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-285">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="2642a-286">その結果、EF Core 5 にアップグレードした後の最初の移行時に、このエンティティ型の既定のテーブルは無視されなくなったため、ドロップが試行されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-286">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-287">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-287">Why</span></span>

<span data-ttu-id="2642a-288">EF Core では、エンティティ型をテーブルとビューの両方に同時にマップできるようになったため、`ToView` は、移行時に無視する必要があることを示す有効なインジケーターではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-288">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-289">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-289">Mitigations</span></span>

<span data-ttu-id="2642a-290">マップされたテーブルを移行の除外対象としてマークするには、次のコードを使用してください。</span><span class="sxs-lookup"><span data-stu-id="2642a-290">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="2642a-291">ToTable(null) により、エンティティ型はテーブルにマップされていないものとしてマークされます</span><span class="sxs-lookup"><span data-stu-id="2642a-291">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="2642a-292">問題 #21172 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-292">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-293">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-293">Old behavior</span></span>

<span data-ttu-id="2642a-294">`ToTable(null)` により、テーブル名が既定値にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-294">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-295">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-295">New behavior</span></span>

<span data-ttu-id="2642a-296">`ToTable(null)` により、エンティティ型はどのテーブルにもマップされていないものとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-296">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-297">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-297">Why</span></span>

<span data-ttu-id="2642a-298">EF Core では、エンティティ型をテーブルとビューの両方に同時にマップできるようになったため、`ToTable(null)` は、どのテーブルにもマップされていないことを示すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-298">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-299">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-299">Mitigations</span></span>

<span data-ttu-id="2642a-300">ビューまたは DbFunction にマップされていない場合、次のコードを使用してテーブル名を既定値にリセットします。</span><span class="sxs-lookup"><span data-stu-id="2642a-300">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="2642a-301">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="2642a-301">Discriminators are read-only</span></span>

[<span data-ttu-id="2642a-302">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-302">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-303">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-303">Old behavior</span></span>

<span data-ttu-id="2642a-304">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="2642a-304">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-305">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-305">New behavior</span></span>

<span data-ttu-id="2642a-306">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-306">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-307">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-307">Why</span></span>

<span data-ttu-id="2642a-308">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-308">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-309">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-309">Mitigations</span></span>

<span data-ttu-id="2642a-310">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="2642a-310">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="2642a-311">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="2642a-311">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="2642a-312">問題 #20294 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-312">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-313">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-313">Old behavior</span></span>

<span data-ttu-id="2642a-314">プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-314">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="2642a-315">たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。</span><span class="sxs-lookup"><span data-stu-id="2642a-315">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-316">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-316">New behavior</span></span>

<span data-ttu-id="2642a-317">プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-317">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-318">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-318">Why</span></span>

<span data-ttu-id="2642a-319">プロバイダー固有のメソッドは、データベース関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-319">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="2642a-320">マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="2642a-320">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="2642a-321">クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-321">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="2642a-322">これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2642a-322">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="2642a-323">InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。</span><span class="sxs-lookup"><span data-stu-id="2642a-323">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="2642a-324">InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-324">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-325">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-325">Mitigations</span></span>

<span data-ttu-id="2642a-326">データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-326">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="2642a-327">IProperty.GetColumnName() は古い形式になりました</span><span class="sxs-lookup"><span data-stu-id="2642a-327">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="2642a-328">問題 #2266 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-328">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-329">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-329">Old behavior</span></span>

<span data-ttu-id="2642a-330">`GetColumnName()` からは、プロパティがマップされている列の名前が返されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-330">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-331">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-331">New behavior</span></span>

<span data-ttu-id="2642a-332">`GetColumnName()` からは引き続きプロパティがマップされている列の名前が返されますが、EF Core 5 では、TPT と、ビューまたは関数への同時マッピングがサポートされており、これらのマッピングにより、同じプロパティに異なる列名が使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-332">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-333">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-333">Why</span></span>

<span data-ttu-id="2642a-334">ユーザーをより正確なオーバーロードに導くために、このメソッド <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> を廃止とマークしました。</span><span class="sxs-lookup"><span data-stu-id="2642a-334">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-335">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-335">Mitigations</span></span>

<span data-ttu-id="2642a-336">特定のテーブルの列名を取得するには、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="2642a-336">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="2642a-337">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="2642a-337">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="2642a-338">問題 #21089 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-338">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-339">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-339">Old behavior</span></span>

<span data-ttu-id="2642a-340">以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。</span><span class="sxs-lookup"><span data-stu-id="2642a-340">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="2642a-341">インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。</span><span class="sxs-lookup"><span data-stu-id="2642a-341">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-342">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-342">New behavior</span></span>

<span data-ttu-id="2642a-343">同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-343">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="2642a-344">これらのインデックスは、モデル内の名前で区別されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-344">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="2642a-345">慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="2642a-345">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-346">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-346">Why</span></span>

<span data-ttu-id="2642a-347">Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="2642a-347">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="2642a-348">この変更により、その方向に新たな一歩を踏み出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2642a-348">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-349">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-349">Mitigations</span></span>

<span data-ttu-id="2642a-350">以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-350">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="2642a-351">EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。</span><span class="sxs-lookup"><span data-stu-id="2642a-351">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="2642a-352">リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています</span><span class="sxs-lookup"><span data-stu-id="2642a-352">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="2642a-353">問題 #11160 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-353">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-354">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-354">Old behavior</span></span>

<span data-ttu-id="2642a-355">以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="2642a-355">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-356">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-356">New behavior</span></span>

<span data-ttu-id="2642a-357">EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-357">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="2642a-358">これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。</span><span class="sxs-lookup"><span data-stu-id="2642a-358">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-359">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-359">Why</span></span>

<span data-ttu-id="2642a-360">コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。</span><span class="sxs-lookup"><span data-stu-id="2642a-360">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-361">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-361">Mitigations</span></span>

<span data-ttu-id="2642a-362">プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。</span><span class="sxs-lookup"><span data-stu-id="2642a-362">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="2642a-363">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="2642a-363">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="2642a-364">イシュー #2568 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-364">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="2642a-365">以前の動作</span><span class="sxs-lookup"><span data-stu-id="2642a-365">Old behavior</span></span>

<span data-ttu-id="2642a-366">5\.0 より前の EF Core では、`INavigation` インターフェイスによって表される 1 つの形式のナビゲーション プロパティのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-366">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="2642a-367">新しい動作</span><span class="sxs-lookup"><span data-stu-id="2642a-367">New behavior</span></span>

<span data-ttu-id="2642a-368">EF Core 5.0 では、"スキップ ナビゲーション" を使用する多対多のリレーションシップが導入されています。</span><span class="sxs-lookup"><span data-stu-id="2642a-368">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="2642a-369">これらは `ISkipNavigation` インターフェイスによって表され、`INavigation` のほとんどの機能は、共通の基底インターフェイスである `INavigationBase` に移されています。</span><span class="sxs-lookup"><span data-stu-id="2642a-369">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="2642a-370">理由</span><span class="sxs-lookup"><span data-stu-id="2642a-370">Why</span></span>

<span data-ttu-id="2642a-371">通常のナビゲーションとスキップ ナビゲーションの機能のほとんどは同じです。</span><span class="sxs-lookup"><span data-stu-id="2642a-371">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="2642a-372">ただし、関連する外部キーはリレーションシップのどちらの端にも直接は存在せず、結合エンティティ内にあるため、スキップ ナビゲーションでの FK へのリレーションシップは通常のナビゲーションとは異なります。</span><span class="sxs-lookup"><span data-stu-id="2642a-372">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="2642a-373">軽減策</span><span class="sxs-lookup"><span data-stu-id="2642a-373">Mitigations</span></span>

<span data-ttu-id="2642a-374">多くの場合は、他の変更なしで新しい基底インターフェイスを使用するようにアプリケーションを切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="2642a-374">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="2642a-375">ただし、外部キー プロパティへのアクセスにナビゲーションが使用されている場合は、アプリケーションのコードを、通常のナビゲーションのみに制限するか、通常とスキップの両方のナビゲーションで適切な処理を行うように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-375">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="2642a-376">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="2642a-376">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="2642a-377">イシュー #15873 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-377">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="2642a-378">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="2642a-378">**Old behavior**</span></span>

<span data-ttu-id="2642a-379">以前は、相関コレクションに続いて `GroupBy` が含まれるクエリおよび `Distinct` を使用する一部のクエリは、実行が許可されていました。</span><span class="sxs-lookup"><span data-stu-id="2642a-379">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="2642a-380">GroupBy の例:</span><span class="sxs-lookup"><span data-stu-id="2642a-380">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="2642a-381">`Distinct` の例 - 具体的には、内部コレクション プロジェクションに主キーが含まれていない `Distinct` クエリ:</span><span class="sxs-lookup"><span data-stu-id="2642a-381">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="2642a-382">これらのクエリは、内部コレクションに重複が含まれている場合は正しくない結果を返す可能性がありましたが、内部コレクション内のすべての要素が一意である場合は正しく動作しました。</span><span class="sxs-lookup"><span data-stu-id="2642a-382">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="2642a-383">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="2642a-383">**New behavior**</span></span>

<span data-ttu-id="2642a-384">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-384">These queries are no longer supported.</span></span> <span data-ttu-id="2642a-385">結果を正しく作成するための十分な情報がないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-385">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="2642a-386">**理由**</span><span class="sxs-lookup"><span data-stu-id="2642a-386">**Why**</span></span>

<span data-ttu-id="2642a-387">相関コレクションのシナリオの場合、正しい親にコレクション エンティティを割り当てるには、エンティティの主キーがわかっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="2642a-387">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="2642a-388">内部コレクションで `GroupBy` または `Distinct` が使用されていない場合は、足りない主キーをプロジェクションに単に追加できます。</span><span class="sxs-lookup"><span data-stu-id="2642a-388">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="2642a-389">ただし、`GroupBy` と `Distinct` の場合は、`GroupBy` または `Distinct` の操作の結果が変わってしまうため実行できません。</span><span class="sxs-lookup"><span data-stu-id="2642a-389">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="2642a-390">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="2642a-390">**Mitigations**</span></span>

<span data-ttu-id="2642a-391">内部コレクションで `GroupBy` または `Distinct` 操作を使用しないようにクエリを書き直し、クライアントでこれらの操作を代わりに実行します。</span><span class="sxs-lookup"><span data-stu-id="2642a-391">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="2642a-392">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="2642a-392">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="2642a-393">イシュー #16314 の追跡</span><span class="sxs-lookup"><span data-stu-id="2642a-393">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="2642a-394">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="2642a-394">**Old behavior**</span></span>

<span data-ttu-id="2642a-395">以前は、たとえば `List<T>` コンストラクターへの引数のような一部の場合には、プロジェクションの内部でクエリ可能型のコレクションを使用できました。</span><span class="sxs-lookup"><span data-stu-id="2642a-395">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="2642a-396">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="2642a-396">**New behavior**</span></span>

<span data-ttu-id="2642a-397">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="2642a-397">These queries are no longer supported.</span></span> <span data-ttu-id="2642a-398">クエリ可能型のオブジェクトを作成できないことが示され、これを修正する方法が提案されている、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2642a-398">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="2642a-399">**理由**</span><span class="sxs-lookup"><span data-stu-id="2642a-399">**Why**</span></span>

<span data-ttu-id="2642a-400">クエリ可能型のオブジェクトを具体化することはできないので、それらは代わりに `List<T>` 型を使用して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="2642a-400">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="2642a-401">このため、型の不一致による例外が発生する場合がよくあります。これはわかりにくいものであり、ユーザーが驚くことがあります。</span><span class="sxs-lookup"><span data-stu-id="2642a-401">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="2642a-402">パターンを認識し、より意味のある例外をスローすることにしました。</span><span class="sxs-lookup"><span data-stu-id="2642a-402">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="2642a-403">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="2642a-403">**Mitigations**</span></span>

<span data-ttu-id="2642a-404">プロジェクション内でクエリ可能オブジェクトの後に `ToList()` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="2642a-404">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
