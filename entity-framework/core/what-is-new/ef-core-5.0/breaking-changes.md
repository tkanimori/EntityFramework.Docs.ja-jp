---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e2537dbc1d5dba48450bd0fea7712054ba2fa622
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503177"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="b1e47-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="b1e47-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="b1e47-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="b1e47-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="b1e47-105">Summary</span></span>

| <span data-ttu-id="b1e47-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="b1e47-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="b1e47-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="b1e47-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="b1e47-108">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="b1e47-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="b1e47-109">中間</span><span class="sxs-lookup"><span data-stu-id="b1e47-109">Medium</span></span>     |
| [<span data-ttu-id="b1e47-110">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="b1e47-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="b1e47-111">中間</span><span class="sxs-lookup"><span data-stu-id="b1e47-111">Medium</span></span>     |
| [<span data-ttu-id="b1e47-112">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="b1e47-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="b1e47-113">中間</span><span class="sxs-lookup"><span data-stu-id="b1e47-113">Medium</span></span>     |
| [<span data-ttu-id="b1e47-114">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="b1e47-115">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-115">Low</span></span>        |
| [<span data-ttu-id="b1e47-116">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="b1e47-117">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-117">Low</span></span>        |
| [<span data-ttu-id="b1e47-118">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="b1e47-119">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-119">Low</span></span>        |
| <span data-ttu-id="b1e47-120">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="b1e47-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="b1e47-121">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-121">Low</span></span>        |
| [<span data-ttu-id="b1e47-122">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="b1e47-123">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-123">Low</span></span>        |
| [<span data-ttu-id="b1e47-124">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="b1e47-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="b1e47-125">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-125">Low</span></span>        |
| [<span data-ttu-id="b1e47-126">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="b1e47-127">Low</span><span class="sxs-lookup"><span data-stu-id="b1e47-127">Low</span></span>        |
| [<span data-ttu-id="b1e47-128">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="b1e47-128">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="b1e47-129">Low</span><span class="sxs-lookup"><span data-stu-id="b1e47-129">Low</span></span>        |
| [<span data-ttu-id="b1e47-130">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="b1e47-130">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="b1e47-131">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-131">Low</span></span>        |
| [<span data-ttu-id="b1e47-132">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="b1e47-132">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="b1e47-133">Low</span><span class="sxs-lookup"><span data-stu-id="b1e47-133">Low</span></span>        |
| [<span data-ttu-id="b1e47-134">リバース エンジニアリングされたモデルをスキャフォールディングするための pluarlizer が含まれるように</span><span class="sxs-lookup"><span data-stu-id="b1e47-134">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="b1e47-135">Low</span><span class="sxs-lookup"><span data-stu-id="b1e47-135">Low</span></span>        |
| [<span data-ttu-id="b1e47-136">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="b1e47-136">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="b1e47-137">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-137">Low</span></span>        |
| [<span data-ttu-id="b1e47-138">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-138">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="b1e47-139">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-139">Low</span></span>        |
| [<span data-ttu-id="b1e47-140">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="b1e47-140">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="b1e47-141">低</span><span class="sxs-lookup"><span data-stu-id="b1e47-141">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="b1e47-142">影響が中程度の変更</span><span class="sxs-lookup"><span data-stu-id="b1e47-142">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="b1e47-143">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="b1e47-143">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="b1e47-144">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-144">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-145">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-145">Old behavior</span></span>

<span data-ttu-id="b1e47-146">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-146">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="b1e47-147">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-147">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-148">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-148">New behavior</span></span>

<span data-ttu-id="b1e47-149">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-149">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="b1e47-150">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-150">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="b1e47-151">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-151">Why</span></span>

<span data-ttu-id="b1e47-152">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="b1e47-152">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-153">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-153">Mitigations</span></span>

<span data-ttu-id="b1e47-154">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-154">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="b1e47-155">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="b1e47-155">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="b1e47-156">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-156">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-157">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-157">Old behavior</span></span>

<span data-ttu-id="b1e47-158">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-158">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="b1e47-159">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-159">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-160">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-160">New behavior</span></span>

<span data-ttu-id="b1e47-161">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-161">APIs for defining query are deprecated.</span></span> <span data-ttu-id="b1e47-162">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-162">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-163">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-163">Why</span></span>

<span data-ttu-id="b1e47-164">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-164">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="b1e47-165">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-165">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="b1e47-166">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="b1e47-166">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="b1e47-167">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-167">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="b1e47-168">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-168">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="b1e47-169">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="b1e47-169">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="b1e47-170">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-170">So we decided to simplify the concept.</span></span> <span data-ttu-id="b1e47-171">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-171">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="b1e47-172">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1e47-172">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-173">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-173">Mitigations</span></span>

<span data-ttu-id="b1e47-174">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-174">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="b1e47-175">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-175">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="b1e47-176">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="b1e47-176">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="b1e47-177">イシュー #2693 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-177">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-178">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-178">Old behavior</span></span>

<span data-ttu-id="b1e47-179">EF Core 3.1 では、非 null 値に意図的に初期化された参照ナビゲーションが、キー値が一致するかどうかに関係なく、データベースからのエンティティ インスタンスによって上書きされることがあります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-179">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="b1e47-180">ただし、それ以外の場合は逆になり、EF Core 3.1 で既存の非 null 値のままになります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-180">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-181">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-181">New behavior</span></span>

<span data-ttu-id="b1e47-182">EF Core 5.0 以降では、非 null 参照ナビゲーションは、クエリから返されたインスタンスによって上書きされることはありません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-182">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="b1e47-183">"_コレクション_" ナビゲーションの空のコレクションへの意図的な初期化は、引き続きサポートされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b1e47-183">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-184">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-184">Why</span></span>

<span data-ttu-id="b1e47-185">参照ナビゲーション プロパティを "空の" エンティティ インスタンスに初期化すると、あいまいな状態になります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-185">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="b1e47-186">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-186">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="b1e47-187">通常、ブログと作成者のクエリにおいては、最初に `Blog` インスタンスを作成した後、データベースから返されたデータに基づいて適切な `Author` インスタンスを設定します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-187">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="b1e47-188">ただし、この場合、`Blog.Author` のすべてのプロパティは、空の `Author` に既に初期化されています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-188">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="b1e47-189">ただし、EF Core には、このインスタンスが "空" であることを認識する方法がありません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-189">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="b1e47-190">そのため、このインスタンスを上書きすると、有効な `Author` が暗黙的に破棄される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-190">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="b1e47-191">したがって、EF Core 5.0 では、既に初期化されているナビゲーションは常に上書きされないようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-191">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="b1e47-192">この新しい動作は、EF6 の動作ともほとんどの場合は一致しますが、調査において EF6 と一致しない場合がいくつか見つかっています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-192">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="b1e47-193">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-193">Mitigations</span></span>

<span data-ttu-id="b1e47-194">この断絶が発生した場合の修正方法は、参照ナビゲーション プロパティの意図的な初期化を止めることです。</span><span class="sxs-lookup"><span data-stu-id="b1e47-194">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="b1e47-195">影響が少ない変更</span><span class="sxs-lookup"><span data-stu-id="b1e47-195">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="b1e47-196">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-196">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="b1e47-197">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-197">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-198">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-198">Old behavior</span></span>

<span data-ttu-id="b1e47-199">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-199">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="b1e47-200">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="b1e47-200">However, it only ever affected database creation.</span></span> <span data-ttu-id="b1e47-201">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="b1e47-201">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="b1e47-202">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/dotnet/efcore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="b1e47-202">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-203">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-203">New behavior</span></span>

<span data-ttu-id="b1e47-204">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-204">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="b1e47-205">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-205">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-206">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-206">Why</span></span>

<span data-ttu-id="b1e47-207">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-207">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-208">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-208">Mitigations</span></span>

<span data-ttu-id="b1e47-209">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-209">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="b1e47-210">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-210">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="b1e47-211">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-211">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-212">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-212">Old behavior</span></span>

<span data-ttu-id="b1e47-213">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-213">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-214">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-214">New behavior</span></span>

<span data-ttu-id="b1e47-215">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-215">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-216">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-216">Why</span></span>

<span data-ttu-id="b1e47-217">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-217">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-218">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-218">Mitigations</span></span>

<span data-ttu-id="b1e47-219">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-219">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="b1e47-220">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-220">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="b1e47-221">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-221">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-222">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-222">Old behavior</span></span>

<span data-ttu-id="b1e47-223">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-223">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-224">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-224">New behavior</span></span>

<span data-ttu-id="b1e47-225">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-225">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-226">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-226">Why</span></span>

<span data-ttu-id="b1e47-227">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-227">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-228">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-228">Mitigations</span></span>

<span data-ttu-id="b1e47-229">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-229">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="b1e47-230">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-230">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="b1e47-231">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-231">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-232">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-232">Old behavior</span></span>

<span data-ttu-id="b1e47-233">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-233">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-234">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-234">New behavior</span></span>

<span data-ttu-id="b1e47-235">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-235">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-236">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-236">Why</span></span>

<span data-ttu-id="b1e47-237">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="b1e47-237">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-238">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-238">Mitigations</span></span>

<span data-ttu-id="b1e47-239">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-239">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="b1e47-240">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/dotnet/efcore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="b1e47-240">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="b1e47-241">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-241">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="b1e47-242">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-242">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-243">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-243">Old behavior</span></span>

<span data-ttu-id="b1e47-244">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="b1e47-244">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-245">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-245">New behavior</span></span>

<span data-ttu-id="b1e47-246">古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="b1e47-246">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-247">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-247">Why</span></span>

<span data-ttu-id="b1e47-248">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-248">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-249">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-249">Mitigations</span></span>

<span data-ttu-id="b1e47-250">新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-250">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="b1e47-251">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="b1e47-251">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="b1e47-252">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-252">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-253">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-253">Old behavior</span></span>

<span data-ttu-id="b1e47-254">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-254">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-255">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-255">New behavior</span></span>

<span data-ttu-id="b1e47-256">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-256">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-257">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-257">Why</span></span>

<span data-ttu-id="b1e47-258">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="b1e47-258">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-259">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-259">Mitigations</span></span>

<span data-ttu-id="b1e47-260">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-260">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="b1e47-261">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-261">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="b1e47-262">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-262">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-263">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-263">Old behavior</span></span>

<span data-ttu-id="b1e47-264">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-264">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-265">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-265">New behavior</span></span>

<span data-ttu-id="b1e47-266">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-266">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="b1e47-267">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-267">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-268">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-268">Why</span></span>

<span data-ttu-id="b1e47-269">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="b1e47-269">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="b1e47-270">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="b1e47-270">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-271">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-271">Mitigations</span></span>

<span data-ttu-id="b1e47-272">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-272">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="b1e47-273">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="b1e47-273">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="b1e47-274">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="b1e47-274">Discriminators are read-only</span></span>

[<span data-ttu-id="b1e47-275">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-275">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-276">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-276">Old behavior</span></span>

<span data-ttu-id="b1e47-277">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-277">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-278">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-278">New behavior</span></span>

<span data-ttu-id="b1e47-279">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-279">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-280">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-280">Why</span></span>

<span data-ttu-id="b1e47-281">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-281">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-282">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-282">Mitigations</span></span>

<span data-ttu-id="b1e47-283">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-283">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="b1e47-284">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="b1e47-284">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="b1e47-285">問題 #20294 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-285">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-286">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-286">Old behavior</span></span>

<span data-ttu-id="b1e47-287">プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-287">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="b1e47-288">たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-288">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-289">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-289">New behavior</span></span>

<span data-ttu-id="b1e47-290">プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-290">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-291">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-291">Why</span></span>

<span data-ttu-id="b1e47-292">プロバイダー固有のメソッドは、データベース関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-292">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="b1e47-293">マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-293">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="b1e47-294">クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-294">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="b1e47-295">これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-295">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="b1e47-296">InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-296">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="b1e47-297">InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-297">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-298">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-298">Mitigations</span></span>

<span data-ttu-id="b1e47-299">データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-299">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="b1e47-300">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="b1e47-300">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="b1e47-301">問題 #21089 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-301">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-302">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-302">Old behavior</span></span>

<span data-ttu-id="b1e47-303">以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。</span><span class="sxs-lookup"><span data-stu-id="b1e47-303">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="b1e47-304">インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-304">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-305">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-305">New behavior</span></span>

<span data-ttu-id="b1e47-306">同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-306">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="b1e47-307">これらのインデックスは、モデル内の名前で区別されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-307">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="b1e47-308">慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-308">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-309">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-309">Why</span></span>

<span data-ttu-id="b1e47-310">Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-310">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="b1e47-311">この変更により、その方向に新たな一歩を踏み出すことができます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-311">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-312">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-312">Mitigations</span></span>

<span data-ttu-id="b1e47-313">以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-313">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="b1e47-314">EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-314">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="b1e47-315">リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています</span><span class="sxs-lookup"><span data-stu-id="b1e47-315">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="b1e47-316">問題 #11160 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-316">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-317">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-317">Old behavior</span></span>

<span data-ttu-id="b1e47-318">以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-318">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-319">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-319">New behavior</span></span>

<span data-ttu-id="b1e47-320">EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-320">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="b1e47-321">これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。</span><span class="sxs-lookup"><span data-stu-id="b1e47-321">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-322">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-322">Why</span></span>

<span data-ttu-id="b1e47-323">コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。</span><span class="sxs-lookup"><span data-stu-id="b1e47-323">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-324">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-324">Mitigations</span></span>

<span data-ttu-id="b1e47-325">プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-325">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="b1e47-326">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="b1e47-326">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="b1e47-327">イシュー #2568 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-327">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="b1e47-328">以前の動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-328">Old behavior</span></span>

<span data-ttu-id="b1e47-329">5\.0 より前の EF Core では、`INavigation` インターフェイスによって表される 1 つの形式のナビゲーション プロパティのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-329">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="b1e47-330">新しい動作</span><span class="sxs-lookup"><span data-stu-id="b1e47-330">New behavior</span></span>

<span data-ttu-id="b1e47-331">EF Core 5.0 では、"スキップ ナビゲーション" を使用する多対多のリレーションシップが導入されています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-331">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="b1e47-332">これらは `ISkipNavigation` インターフェイスによって表され、`INavigation` のほとんどの機能は、共通の基底インターフェイスである `INavigationBase` に移されています。</span><span class="sxs-lookup"><span data-stu-id="b1e47-332">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="b1e47-333">理由</span><span class="sxs-lookup"><span data-stu-id="b1e47-333">Why</span></span>

<span data-ttu-id="b1e47-334">通常のナビゲーションとスキップ ナビゲーションの機能のほとんどは同じです。</span><span class="sxs-lookup"><span data-stu-id="b1e47-334">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="b1e47-335">ただし、関連する外部キーはリレーションシップのどちらの端にも直接は存在せず、結合エンティティ内にあるため、スキップ ナビゲーションでの FK へのリレーションシップは通常のナビゲーションとは異なります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-335">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="b1e47-336">軽減策</span><span class="sxs-lookup"><span data-stu-id="b1e47-336">Mitigations</span></span>

<span data-ttu-id="b1e47-337">多くの場合は、他の変更なしで新しい基底インターフェイスを使用するようにアプリケーションを切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-337">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="b1e47-338">ただし、外部キー プロパティへのアクセスにナビゲーションが使用されている場合は、アプリケーションのコードを、通常のナビゲーションのみに制限するか、通常とスキップの両方のナビゲーションで適切な処理を行うように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-338">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="b1e47-339">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="b1e47-339">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="b1e47-340">イシュー #15873 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-340">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="b1e47-341">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="b1e47-341">**Old behavior**</span></span>

<span data-ttu-id="b1e47-342">以前は、相関コレクションに続いて `GroupBy` が含まれるクエリおよび `Distinct` を使用する一部のクエリは、実行が許可されていました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-342">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="b1e47-343">GroupBy の例:</span><span class="sxs-lookup"><span data-stu-id="b1e47-343">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="b1e47-344">`Distinct` の例 - 具体的には、内部コレクション プロジェクションに主キーが含まれていない `Distinct` クエリ:</span><span class="sxs-lookup"><span data-stu-id="b1e47-344">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="b1e47-345">これらのクエリは、内部コレクションに重複が含まれている場合は正しくない結果を返す可能性がありましたが、内部コレクション内のすべての要素が一意である場合は正しく動作しました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-345">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="b1e47-346">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="b1e47-346">**New behavior**</span></span>

<span data-ttu-id="b1e47-347">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-347">These queries are no longer supported.</span></span> <span data-ttu-id="b1e47-348">結果を正しく作成するための十分な情報がないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-348">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="b1e47-349">**理由**</span><span class="sxs-lookup"><span data-stu-id="b1e47-349">**Why**</span></span>

<span data-ttu-id="b1e47-350">相関コレクションのシナリオの場合、正しい親にコレクション エンティティを割り当てるには、エンティティの主キーがわかっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-350">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="b1e47-351">内部コレクションで `GroupBy` または `Distinct` が使用されていない場合は、足りない主キーをプロジェクションに単に追加できます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-351">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="b1e47-352">ただし、`GroupBy` と `Distinct` の場合は、`GroupBy` または `Distinct` の操作の結果が変わってしまうため実行できません。</span><span class="sxs-lookup"><span data-stu-id="b1e47-352">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="b1e47-353">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="b1e47-353">**Mitigations**</span></span>

<span data-ttu-id="b1e47-354">内部コレクションで `GroupBy` または `Distinct` 操作を使用しないようにクエリを書き直し、クライアントでこれらの操作を代わりに実行します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-354">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="b1e47-355">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="b1e47-355">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="b1e47-356">イシュー #16314 の追跡</span><span class="sxs-lookup"><span data-stu-id="b1e47-356">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="b1e47-357">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="b1e47-357">**Old behavior**</span></span>

<span data-ttu-id="b1e47-358">以前は、たとえば `List<T>` コンストラクターへの引数のような一部の場合には、プロジェクションの内部でクエリ可能型のコレクションを使用できました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-358">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="b1e47-359">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="b1e47-359">**New behavior**</span></span>

<span data-ttu-id="b1e47-360">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-360">These queries are no longer supported.</span></span> <span data-ttu-id="b1e47-361">クエリ可能型のオブジェクトを作成できないことが示され、これを修正する方法が提案されている、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-361">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="b1e47-362">**理由**</span><span class="sxs-lookup"><span data-stu-id="b1e47-362">**Why**</span></span>

<span data-ttu-id="b1e47-363">クエリ可能型のオブジェクトを具体化することはできないので、それらは代わりに `List<T>` 型を使用して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="b1e47-363">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="b1e47-364">このため、型の不一致による例外が発生する場合がよくあります。これはわかりにくいものであり、ユーザーが驚くことがあります。</span><span class="sxs-lookup"><span data-stu-id="b1e47-364">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="b1e47-365">パターンを認識し、より意味のある例外をスローすることにしました。</span><span class="sxs-lookup"><span data-stu-id="b1e47-365">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="b1e47-366">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="b1e47-366">**Mitigations**</span></span>

<span data-ttu-id="b1e47-367">プロジェクション内でクエリ可能オブジェクトの後に `ToList()` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="b1e47-367">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
