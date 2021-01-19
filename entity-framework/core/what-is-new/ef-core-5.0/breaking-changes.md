---
title: EF Core 5.0 での破壊的変更 - EF Core
description: Entity Framework Core 5.0 で導入された重大な変更の完全な一覧
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 4a463e785edaceaf5dd96164c39e2cc9b5f86de4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128746"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="be4bf-103">EF Core 5.0 での破壊的変更</span><span class="sxs-lookup"><span data-stu-id="be4bf-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="be4bf-104">次の API と動作の変更により、EF Core 5.0.0 に更新されている既存のアプリケーションが破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="be4bf-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="be4bf-105">Summary</span></span>

| <span data-ttu-id="be4bf-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="be4bf-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="be4bf-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="be4bf-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="be4bf-108">EF Core 5.0 では .NET Framework がサポートされていません</span><span class="sxs-lookup"><span data-stu-id="be4bf-108">EF Core 5.0 does not support .NET Framework</span></span>](#netstandard21)                                                                         | <span data-ttu-id="be4bf-109">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-109">Medium</span></span>     |
| [<span data-ttu-id="be4bf-110">IProperty.GetColumnName() は古い形式になりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-110">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="be4bf-111">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-111">Medium</span></span>     |
| [<span data-ttu-id="be4bf-112">10 進数には有効桁数と小数点以下桁数が必要です</span><span class="sxs-lookup"><span data-stu-id="be4bf-112">Precision and scale are required for decimals</span></span>](#decimals)                                                                            | <span data-ttu-id="be4bf-113">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-113">Medium</span></span>     |
| [<span data-ttu-id="be4bf-114">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="be4bf-114">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="be4bf-115">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-115">Medium</span></span>     |
| [<span data-ttu-id="be4bf-116">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="be4bf-116">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="be4bf-117">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-117">Medium</span></span>     |
| [<span data-ttu-id="be4bf-118">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="be4bf-118">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="be4bf-119">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-119">Medium</span></span>     |
| [<span data-ttu-id="be4bf-120">移行による ToView() の処理方法が変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-120">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="be4bf-121">Medium</span><span class="sxs-lookup"><span data-stu-id="be4bf-121">Medium</span></span>     |
| [<span data-ttu-id="be4bf-122">ToTable(null) により、エンティティ型はテーブルにマップされていないものとしてマークされます</span><span class="sxs-lookup"><span data-stu-id="be4bf-122">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="be4bf-123">中間</span><span class="sxs-lookup"><span data-stu-id="be4bf-123">Medium</span></span>     |
| [<span data-ttu-id="be4bf-124">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-124">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="be4bf-125">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-125">Low</span></span>        |
| [<span data-ttu-id="be4bf-126">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-126">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="be4bf-127">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-127">Low</span></span>        |
| [<span data-ttu-id="be4bf-128">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-128">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="be4bf-129">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-129">Low</span></span>        |
| <span data-ttu-id="be4bf-130">[Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="be4bf-130">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="be4bf-131">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-131">Low</span></span>        |
| [<span data-ttu-id="be4bf-132">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-132">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="be4bf-133">Low</span><span class="sxs-lookup"><span data-stu-id="be4bf-133">Low</span></span>        |
| [<span data-ttu-id="be4bf-134">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="be4bf-134">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="be4bf-135">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-135">Low</span></span>        |
| [<span data-ttu-id="be4bf-136">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-136">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="be4bf-137">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-137">Low</span></span>        |
| [<span data-ttu-id="be4bf-138">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="be4bf-138">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="be4bf-139">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-139">Low</span></span>        |
| [<span data-ttu-id="be4bf-140">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="be4bf-140">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="be4bf-141">Low</span><span class="sxs-lookup"><span data-stu-id="be4bf-141">Low</span></span>        |
| [<span data-ttu-id="be4bf-142">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="be4bf-142">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="be4bf-143">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-143">Low</span></span>        |
| [<span data-ttu-id="be4bf-144">リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています</span><span class="sxs-lookup"><span data-stu-id="be4bf-144">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="be4bf-145">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-145">Low</span></span>        |
| [<span data-ttu-id="be4bf-146">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="be4bf-146">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="be4bf-147">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-147">Low</span></span>        |
| [<span data-ttu-id="be4bf-148">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-148">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="be4bf-149">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-149">Low</span></span>        |
| [<span data-ttu-id="be4bf-150">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="be4bf-150">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="be4bf-151">低</span><span class="sxs-lookup"><span data-stu-id="be4bf-151">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="be4bf-152">影響が中程度の変更</span><span class="sxs-lookup"><span data-stu-id="be4bf-152">Medium-impact changes</span></span>

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a><span data-ttu-id="be4bf-153">EF Core 5.0 では .NET Framework がサポートされていません</span><span class="sxs-lookup"><span data-stu-id="be4bf-153">EF Core 5.0 does not support .NET Framework</span></span>

[<span data-ttu-id="be4bf-154">問題 #15498 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-154">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-155">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-155">Old behavior</span></span>

<span data-ttu-id="be4bf-156">EF Core 3.1 のターゲットは .NET Framework によってサポートされている .NET Standard 2.0 です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-156">EF Core 3.1 targets .NET Standard 2.0, which is supported by .NET Framework.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-157">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-157">New behavior</span></span>

<span data-ttu-id="be4bf-158">EF Core 5.0 のターゲットは、.NET Framework でサポートされていない .NET Standard 2.1 です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-158">EF Core 5.0 targets .NET Standard 2.1, which is not supported by .NET Framework.</span></span> <span data-ttu-id="be4bf-159">つまり、EF Core 5.0 を .NET Framework アプリケーションで使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-159">This means EF Core 5.0 cannot be used with .NET Framework applications.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-160">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-160">Why</span></span>

<span data-ttu-id="be4bf-161">これは、1 つの .NET ターゲット フレームワークへの統合を目的とした .NET チーム全体でのより広範な移行の一部です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-161">This is part of the wider movement across .NET teams aimed at unification to a single .NET target framework.</span></span> <span data-ttu-id="be4bf-162">詳細については、「[.NET Standardの将来](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="be4bf-162">For more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-163">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-163">Mitigations</span></span>

<span data-ttu-id="be4bf-164">.NET Framework アプリケーションでは、[長期的なサポート (LTS) リリース](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)である EF Core 3.1 を引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-164">.NET Framework applications can continue to use EF Core 3.1, which is a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="be4bf-165">あるいは、.NET Core 2.1、.NET Core 3.1、.NET 5 (これらすべてで、.NET Standard 2.1 がサポートされています) を使用するようにアプリケーションを更新することもできます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-165">Alternately, applications can be updated to use .NET Core 2.1, .NET Core 3.1, or .NET 5, all of which support .NET Standard 2.1.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="be4bf-166">IProperty.GetColumnName() は古い形式になりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-166">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="be4bf-167">問題 #2266 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-167">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-168">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-168">Old behavior</span></span>

<span data-ttu-id="be4bf-169">`GetColumnName()` からは、プロパティがマップされている列の名前が返されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-169">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-170">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-170">New behavior</span></span>

<span data-ttu-id="be4bf-171">`GetColumnName()` からは引き続きプロパティがマップされている列の名前が返されますが、EF Core 5 では、TPT と、ビューまたは関数への同時マッピングがサポートされており、これらのマッピングにより、同じプロパティに異なる列名が使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-171">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-172">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-172">Why</span></span>

<span data-ttu-id="be4bf-173">ユーザーをより正確なオーバーロードに導くために、このメソッド <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> を廃止とマークしました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-173">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-174">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-174">Mitigations</span></span>

<span data-ttu-id="be4bf-175">特定のテーブルの列名を取得するには、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-175">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a><span data-ttu-id="be4bf-176">10 進数には有効桁数と小数点以下桁数が必要です</span><span class="sxs-lookup"><span data-stu-id="be4bf-176">Precision and scale are required for decimals</span></span>

[<span data-ttu-id="be4bf-177">問題 #19293 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-177">Tracking Issue #19293</span></span>](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-178">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-178">Old behavior</span></span>

<span data-ttu-id="be4bf-179"><xref:Microsoft.Data.SqlClient.SqlParameter> オブジェクトの有効桁数と小数点以下桁数は、通常、EF Core によって設定されませんでした。</span><span class="sxs-lookup"><span data-stu-id="be4bf-179">EF Core did not normally set precision and scale on <xref:Microsoft.Data.SqlClient.SqlParameter> objects.</span></span> <span data-ttu-id="be4bf-180">これは、完全な有効桁数と小数点以下桁数が SQL Server に送信され、その時点で SQL Server によりデータベース列の有効桁数と小数点以下桁数に基づいて丸められていたことを意味します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-180">This means the full precision and scale was sent to SQL Server, at which point SQL Server would round based on the precision and scale of the database column.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-181">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-181">New behavior</span></span>

<span data-ttu-id="be4bf-182">有効桁数と小数点以下桁数は、EF Core によって、EF Core モデル内のプロパティに対して構成された値を使用してパラメーター上に設定されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-182">EF Core now sets precision and scale on parameters using the values configured for properties in the EF Core model.</span></span> <span data-ttu-id="be4bf-183">これは、SqlClient で丸め処理が行われることを意味します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-183">This means rounding now happens in SqlClient.</span></span> <span data-ttu-id="be4bf-184">その結果、構成された有効桁数と小数点以下桁数がデータベースの有効桁数と小数点以下桁数と一致しない場合は、丸めの表示が変わることがあります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-184">Consequentially, if the configured precision and scale do not match the database precision and scale, then the rounding seen may change.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-185">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-185">Why</span></span>

<span data-ttu-id="be4bf-186">Always Encrypted などの新しい SQL Server 機能を使用するには、パラメーター ファセットが完全に指定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-186">Newer SQL Server features, including Always Encrypted, require that parameter facets are fully specified.</span></span> <span data-ttu-id="be4bf-187">さらに、10 進値を切り捨てるのではなく、丸めるように SqlClient に変更が加えられ、SQL Server の動作と一致することになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-187">In addition, SqlClient made a change to round instead of truncate decimal values, thereby matching the SQL Server behavior.</span></span> <span data-ttu-id="be4bf-188">これにより、適切に構成された 10 進数に対する動作を変更することなしに、これらのファセットを EF Core で設定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-188">This made it possible for EF Core to set these facets without changing the behavior for correctly configured decimals.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-189">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-189">Mitigations</span></span>

<span data-ttu-id="be4bf-190">有効桁数と小数点以下桁数を含む型名を使用して、10 進数のプロパティをマップします。</span><span class="sxs-lookup"><span data-stu-id="be4bf-190">Map your decimal properties using a type name that includes precision and scale.</span></span> <span data-ttu-id="be4bf-191">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-191">For example:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

<span data-ttu-id="be4bf-192">または、モデル構築 API で `HasPrecision` を使用します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-192">Or use `HasPrecision` in the model building APIs.</span></span> <span data-ttu-id="be4bf-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-193">For example:</span></span>

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="be4bf-194">プリンシパルへのナビゲーションと依存関係へのナビゲーションでは、Required のセマンティクスが異なります</span><span class="sxs-lookup"><span data-stu-id="be4bf-194">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="be4bf-195">問題 #17286 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-195">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-196">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-196">Old behavior</span></span>

<span data-ttu-id="be4bf-197">必要に応じて、プリンシパルへのナビゲーションのみを構成できます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-197">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="be4bf-198">したがって、依存関係 (外部キーを含むエンティティ) へのナビゲーションで `RequiredAttribute` を使用すると、定義エンティティ型上に外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-198">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-199">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-199">New behavior</span></span>

<span data-ttu-id="be4bf-200">必要な依存関係に対するサポートが追加されたため、必要に応じて任意の参照ナビゲーションをマークできるようになりました。つまり、上記の例では、外部キーはリレーションシップのもう一方の側で定義され、プロパティは必須としてマークされません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-200">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="be4bf-201">依存関係の終了を指定する前の `IsRequired` の呼び出しがあいまいになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-201">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="be4bf-202">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-202">Why</span></span>

<span data-ttu-id="be4bf-203">必要な依存関係のサポートを有効にするために、新しい動作が必要です ([#12100](https://github.com/dotnet/efcore/issues/12100) を参照)。</span><span class="sxs-lookup"><span data-stu-id="be4bf-203">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-204">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-204">Mitigations</span></span>

<span data-ttu-id="be4bf-205">依存関係へのナビゲーションから `RequiredAttribute` を削除し、代わりにプリンシパルへのナビゲーションに配置するか、または `OnModelCreating` でリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-205">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="be4bf-206">クエリの定義は、プロバイダー固有のメソッドに置き換えられます</span><span class="sxs-lookup"><span data-stu-id="be4bf-206">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="be4bf-207">問題 #18903 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-207">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-208">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-208">Old behavior</span></span>

<span data-ttu-id="be4bf-209">エンティティ型は、コア レベルでクエリを定義するためにマップされました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-209">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="be4bf-210">エンティティ型のクエリ ルートでエンティティ型が使用されたときはいつでも、任意のプロバイダーのクエリの定義に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-210">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-211">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-211">New behavior</span></span>

<span data-ttu-id="be4bf-212">クエリを定義するための API は非推奨とされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-212">APIs for defining query are deprecated.</span></span> <span data-ttu-id="be4bf-213">新しいプロバイダー固有の API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-213">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-214">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-214">Why</span></span>

<span data-ttu-id="be4bf-215">クエリでクエリ ルートが使用される場合は常に、クエリの定義が置換クエリとして実装されましたが、いくつかの問題がありました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-215">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="be4bf-216">クエリの定義で `Select` メソッドの `new { ... }` を使用してエンティティ型を射影している場合、それをエンティティとして識別するには追加の作業が必要で、EF Core によりクエリで名目的な型が処理される方法と矛盾していました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-216">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="be4bf-217">リレーショナル プロバイダーの場合は、LINQ 式形式で SQL 文字列を渡すために `FromSql` が引き続き必要です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-217">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="be4bf-218">最初に定義されたクエリは、キーなしエンティティ用のメモリ内プロバイダーで使用されるクライアント側のビュー (リレーショナル データベースのデータベース ビューに似ています) として導入されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-218">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="be4bf-219">このような定義により、メモリ内データベースに対してアプリケーションを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-219">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="be4bf-220">その後、それらは広く適用できるようになりました。これは有用でしたが、一貫性がなく、動作を理解するのが困難でした。</span><span class="sxs-lookup"><span data-stu-id="be4bf-220">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="be4bf-221">そのため、概念を簡略化することにしました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-221">So we decided to simplify the concept.</span></span> <span data-ttu-id="be4bf-222">LINQ ベースのクエリをメモリ内プロバイダーに限定して定義し、異なる方法で処理しました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-222">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="be4bf-223">詳細については、[この問題](https://github.com/dotnet/efcore/issues/20023)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="be4bf-223">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-224">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-224">Mitigations</span></span>

<span data-ttu-id="be4bf-225">リレーショナル プロバイダーの場合は、`OnModelCreating` で `ToSqlQuery` メソッドを使用し、エンティティ型に使用する SQL 文字列を渡します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-225">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="be4bf-226">メモリ内プロバイダーの場合は、`OnModelCreating` で `ToInMemoryQuery` メソッドを使用し、エンティティ型に使用する LINQ クエリを渡します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-226">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="be4bf-227">非 null 参照ナビゲーションがクエリによって上書きされません</span><span class="sxs-lookup"><span data-stu-id="be4bf-227">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="be4bf-228">イシュー #2693 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-228">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-229">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-229">Old behavior</span></span>

<span data-ttu-id="be4bf-230">EF Core 3.1 では、非 null 値に意図的に初期化された参照ナビゲーションが、キー値が一致するかどうかに関係なく、データベースからのエンティティ インスタンスによって上書きされることがあります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-230">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="be4bf-231">ただし、それ以外の場合は逆になり、EF Core 3.1 で既存の非 null 値のままになります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-231">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-232">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-232">New behavior</span></span>

<span data-ttu-id="be4bf-233">EF Core 5.0 以降では、非 null 参照ナビゲーションは、クエリから返されたインスタンスによって上書きされることはありません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-233">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="be4bf-234">"_コレクション_" ナビゲーションの空のコレクションへの意図的な初期化は、引き続きサポートされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="be4bf-234">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-235">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-235">Why</span></span>

<span data-ttu-id="be4bf-236">参照ナビゲーション プロパティを "空の" エンティティ インスタンスに初期化すると、あいまいな状態になります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-236">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="be4bf-237">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-237">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="be4bf-238">通常、ブログと作成者のクエリにおいては、最初に `Blog` インスタンスを作成した後、データベースから返されたデータに基づいて適切な `Author` インスタンスを設定します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-238">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="be4bf-239">ただし、この場合、`Blog.Author` のすべてのプロパティは、空の `Author` に既に初期化されています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-239">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="be4bf-240">ただし、EF Core には、このインスタンスが "空" であることを認識する方法がありません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-240">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="be4bf-241">そのため、このインスタンスを上書きすると、有効な `Author` が暗黙的に破棄される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-241">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="be4bf-242">したがって、EF Core 5.0 では、既に初期化されているナビゲーションは常に上書きされないようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-242">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="be4bf-243">この新しい動作は、EF6 の動作ともほとんどの場合は一致しますが、調査において EF6 と一致しない場合がいくつか見つかっています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-243">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-244">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-244">Mitigations</span></span>

<span data-ttu-id="be4bf-245">この断絶が発生した場合の修正方法は、参照ナビゲーション プロパティの意図的な初期化を止めることです。</span><span class="sxs-lookup"><span data-stu-id="be4bf-245">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="be4bf-246">移行による ToView() の処理方法が変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-246">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="be4bf-247">問題 #2725 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-247">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-248">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-248">Old behavior</span></span>

<span data-ttu-id="be4bf-249">`ToView(string)` を呼び出すと、エンティティ型がビューにマップされるだけでなく、移行時に無視されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-249">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-250">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-250">New behavior</span></span>

<span data-ttu-id="be4bf-251">`ToView(string)` により、エンティティ型はビューにマップされるだけでなく、テーブルにマップされていないものとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-251">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="be4bf-252">その結果、EF Core 5 にアップグレードした後の最初の移行時に、このエンティティ型の既定のテーブルは無視されなくなったため、ドロップが試行されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-252">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-253">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-253">Why</span></span>

<span data-ttu-id="be4bf-254">EF Core では、エンティティ型をテーブルとビューの両方に同時にマップできるようになったため、`ToView` は、移行時に無視する必要があることを示す有効なインジケーターではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-254">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-255">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-255">Mitigations</span></span>

<span data-ttu-id="be4bf-256">マップされたテーブルを移行の除外対象としてマークするには、次のコードを使用してください。</span><span class="sxs-lookup"><span data-stu-id="be4bf-256">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="be4bf-257">ToTable(null) により、エンティティ型はテーブルにマップされていないものとしてマークされます</span><span class="sxs-lookup"><span data-stu-id="be4bf-257">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="be4bf-258">問題 #21172 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-258">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-259">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-259">Old behavior</span></span>

<span data-ttu-id="be4bf-260">`ToTable(null)` により、テーブル名が既定値にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-260">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-261">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-261">New behavior</span></span>

<span data-ttu-id="be4bf-262">`ToTable(null)` により、エンティティ型はどのテーブルにもマップされていないものとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-262">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-263">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-263">Why</span></span>

<span data-ttu-id="be4bf-264">EF Core では、エンティティ型をテーブルとビューの両方に同時にマップできるようになったため、`ToTable(null)` は、どのテーブルにもマップされていないことを示すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-264">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-265">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-265">Mitigations</span></span>

<span data-ttu-id="be4bf-266">ビューまたは DbFunction にマップされていない場合、次のコードを使用してテーブル名を既定値にリセットします。</span><span class="sxs-lookup"><span data-stu-id="be4bf-266">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a><span data-ttu-id="be4bf-267">影響が少ない変更</span><span class="sxs-lookup"><span data-stu-id="be4bf-267">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="be4bf-268">SQLite NTS 拡張機能から HasGeometricDimension メソッドが削除されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-268">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="be4bf-269">イシュー #14257 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-269">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-270">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-270">Old behavior</span></span>

<span data-ttu-id="be4bf-271">HasGeometricDimension は、geometry 列で追加のディメンション (Z および M) を有効にするために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-271">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="be4bf-272">ただし、この影響を受けていたのはデータベースの作成だけです。</span><span class="sxs-lookup"><span data-stu-id="be4bf-272">However, it only ever affected database creation.</span></span> <span data-ttu-id="be4bf-273">追加のディメンションで値をクエリするためにそれを指定する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="be4bf-273">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="be4bf-274">また、追加のディメンションで値を挿入または更新するときもそれは正しく機能しませんでした ([#14257](https://github.com/dotnet/efcore/issues/14257) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="be4bf-274">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-275">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-275">New behavior</span></span>

<span data-ttu-id="be4bf-276">追加のディメンション (Z および M) で geometry 値の挿入と更新を有効にするには、列の型名の一部としてディメンションを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-276">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="be4bf-277">この API は、SpatiaLite の AddGeometryColumn 関数の基になる動作により厳密に一致します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-277">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-278">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-278">Why</span></span>

<span data-ttu-id="be4bf-279">列の型でディメンションを指定した後に HasGeometricDimension を使用することは不要であり冗長なので、HasGeometricDimension を完全に削除しました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-279">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-280">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-280">Mitigations</span></span>

<span data-ttu-id="be4bf-281">`HasColumnType` を使用してディメンションを指定します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-281">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="be4bf-282">Cosmos: パーティション キーが主キーに追加されるようになりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-282">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="be4bf-283">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-283">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-284">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-284">Old behavior</span></span>

<span data-ttu-id="be4bf-285">パーティション キー プロパティは、`id` を含む代替キーにのみ追加されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-285">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-286">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-286">New behavior</span></span>

<span data-ttu-id="be4bf-287">規則に従って、パーティション キー プロパティも主キーに追加されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-287">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-288">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-288">Why</span></span>

<span data-ttu-id="be4bf-289">この変更により、モデルと Azure Cosmos DB セマンティクスとの連携が向上し、`Find` と一部のクエリのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-289">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-290">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-290">Mitigations</span></span>

<span data-ttu-id="be4bf-291">パーティション キー プロパティを主キーに追加しないようにするには、`OnModelCreating` で構成します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-291">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="be4bf-292">Cosmos: `id` プロパティの名前が `__id` に変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-292">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="be4bf-293">問題 #17751 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-293">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-294">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-294">Old behavior</span></span>

<span data-ttu-id="be4bf-295">`id` JSON プロパティにマップされたシャドウ プロパティも `id` に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-295">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-296">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-296">New behavior</span></span>

<span data-ttu-id="be4bf-297">規則に従って作成されたシャドウ プロパティは `__id` という名前になりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-297">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-298">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-298">Why</span></span>

<span data-ttu-id="be4bf-299">この変更により、`id` プロパティがエンティティ型の既存のプロパティと競合する可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-299">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-300">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-300">Mitigations</span></span>

<span data-ttu-id="be4bf-301">3\.x の動作に戻るには、`OnModelCreating` で `id` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-301">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="be4bf-302">Cosmos: byte[] が、数値配列ではなく base64 文字列として格納されるようになりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-302">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="be4bf-303">問題 #17306 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-303">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-304">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-304">Old behavior</span></span>

<span data-ttu-id="be4bf-305">byte[] 型のプロパティは、数値配列として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-305">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-306">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-306">New behavior</span></span>

<span data-ttu-id="be4bf-307">byte[] 型のプロパティは、base64 文字列として格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-307">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-308">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-308">Why</span></span>

<span data-ttu-id="be4bf-309">この byte[] の表現は期待に沿ったもので、主要な JSON シリアル化ライブラリの既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-309">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-310">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-310">Mitigations</span></span>

<span data-ttu-id="be4bf-311">数値配列として格納されている既存のデータは、引き続き正しくクエリされますが、現在、挿入動作を元に戻す方法はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-311">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="be4bf-312">この制限がシナリオの妨げとなっている場合は、[この問題](https://github.com/dotnet/efcore/issues/17306)にコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="be4bf-312">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="be4bf-313">Cosmos: GetPropertyName と SetPropertyName の名前が変更されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-313">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="be4bf-314">問題 #17874 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-314">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-315">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-315">Old behavior</span></span>

<span data-ttu-id="be4bf-316">以前は、拡張メソッドは、`GetPropertyName` および `SetPropertyName` と呼ばれていました</span><span class="sxs-lookup"><span data-stu-id="be4bf-316">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-317">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-317">New behavior</span></span>

<span data-ttu-id="be4bf-318">古い API は削除され、新しいメソッドである `GetJsonPropertyName` と `SetJsonPropertyName` が追加されました</span><span class="sxs-lookup"><span data-stu-id="be4bf-318">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-319">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-319">Why</span></span>

<span data-ttu-id="be4bf-320">この変更により、これらのメソッドの構成に関するあいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-320">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-321">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-321">Mitigations</span></span>

<span data-ttu-id="be4bf-322">新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-322">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="be4bf-323">エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更されると、値ジェネレーターが呼び出されます</span><span class="sxs-lookup"><span data-stu-id="be4bf-323">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="be4bf-324">問題 #15289 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-324">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-325">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-325">Old behavior</span></span>

<span data-ttu-id="be4bf-326">値ジェネレーターは、エンティティの状態が Added に変更されたときにのみ呼び出されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-326">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-327">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-327">New behavior</span></span>

<span data-ttu-id="be4bf-328">値ジェネレーターは、エンティティの状態が Detached から Unchanged、Updated、または Deleted に変更され、プロパティに既定値が含まれているときに呼び出されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-328">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-329">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-329">Why</span></span>

<span data-ttu-id="be4bf-330">この変更は、データストアに保持されていない、常にクライアントで値が生成されるプロパティのエクスペリエンスを向上させるために必要でした。</span><span class="sxs-lookup"><span data-stu-id="be4bf-330">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-331">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-331">Mitigations</span></span>

<span data-ttu-id="be4bf-332">値ジェネレーターが呼び出されないようにするには、状態が変わる前に、既定値以外の値をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-332">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="be4bf-333">IMigrationsModelDiffer で IRelationalModel が使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-333">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="be4bf-334">問題 #20305 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-334">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-335">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-335">Old behavior</span></span>

<span data-ttu-id="be4bf-336">`IMigrationsModelDiffer` API は `IModel` を使用して定義されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-336">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-337">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-337">New behavior</span></span>

<span data-ttu-id="be4bf-338">`IMigrationsModelDiffer` API で `IRelationalModel` が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-338">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="be4bf-339">ただし、`IModel` はアプリケーションの一部であり、より重大な変更を加えることなく Entity Framework でこれを変更することはできないため、モデル スナップショットには引き続きこのコードのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-339">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-340">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-340">Why</span></span>

<span data-ttu-id="be4bf-341">`IRelationalModel` は、データベース スキーマの新しく追加された表現です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-341">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="be4bf-342">違いを見つけるためにこれを使用する方が高速で正確です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-342">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-343">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-343">Mitigations</span></span>

<span data-ttu-id="be4bf-344">次のコードを使用して、`snapshot` のモデルを `context` のモデルと比較します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-344">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="be4bf-345">6\.0 でこのエクスペリエンスを向上させることを計画しています ([#22031](https://github.com/dotnet/efcore/issues/22031) を参照)</span><span class="sxs-lookup"><span data-stu-id="be4bf-345">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="be4bf-346">識別子が読み取り専用です</span><span class="sxs-lookup"><span data-stu-id="be4bf-346">Discriminators are read-only</span></span>

[<span data-ttu-id="be4bf-347">問題 #21154 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-347">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-348">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-348">Old behavior</span></span>

<span data-ttu-id="be4bf-349">`SaveChanges` を呼び出す前に識別子の値を変更することができました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-349">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-350">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-350">New behavior</span></span>

<span data-ttu-id="be4bf-351">上記のケースでは、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-351">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-352">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-352">Why</span></span>

<span data-ttu-id="be4bf-353">EF では、追跡中のエンティティ型を変更することは想定されていません。したがって、識別子の値を変更するとコンテキストが不整合な状態になり、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-353">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-354">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-354">Mitigations</span></span>

<span data-ttu-id="be4bf-355">識別子の値を変更する必要があり、`SaveChanges` を呼び出した直後にコンテキストが破棄される場合は、識別子を変更可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-355">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="be4bf-356">プロバイダー固有の EF.Functions メソッドによって InMemory プロバイダーに対してスローされます</span><span class="sxs-lookup"><span data-stu-id="be4bf-356">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="be4bf-357">問題 #20294 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-357">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-358">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-358">Old behavior</span></span>

<span data-ttu-id="be4bf-359">プロバイダー固有の EF.Functions メソッドには、InMemory プロバイダーでのその実行が可能な、クライアント実行の実装が含まれていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-359">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="be4bf-360">たとえば、`EF.Functions.DateDiffDay` は、Sql Server 固有のメソッドであり、InMemory プロバイダーで動作します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-360">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-361">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-361">New behavior</span></span>

<span data-ttu-id="be4bf-362">プロバイダー固有のメソッドは、クライアント側で評価されることをブロックするために、メソッド本体で例外をスローするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-362">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-363">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-363">Why</span></span>

<span data-ttu-id="be4bf-364">プロバイダー固有のメソッドは、データベース関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-364">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="be4bf-365">マップされたデータベース関数によって実行される計算は、LINQ のクライアント側で常にレプリケートされるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-365">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="be4bf-366">クライアントで同じメソッドを実行すると、サーバーからの結果が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-366">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="be4bf-367">これらのメソッドは、特定のデータベース関数への変換を目的として LINQ で使用されるため、クライアント側での評価は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-367">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="be4bf-368">InMemory プロバイダーは "*データベース*" が異なるため、このプロバイダーにはこれらのメソッドを使用できません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-368">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="be4bf-369">InMemory プロバイダー、またはこれらのメソッドを変換しない他のプロバイダーに対してこれらを実行しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-369">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-370">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-370">Mitigations</span></span>

<span data-ttu-id="be4bf-371">データベース関数の動作を正確に模倣する方法がないため、それらが含まれるクエリを、実稼働環境と同じ種類のデータベースに対してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-371">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="be4bf-372">IndexBuilder.HasName が古い形式に</span><span class="sxs-lookup"><span data-stu-id="be4bf-372">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="be4bf-373">問題 #21089 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-373">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-374">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-374">Old behavior</span></span>

<span data-ttu-id="be4bf-375">以前は、特定のプロパティ セットに対して定義できるインデックスは 1 つだけでした。</span><span class="sxs-lookup"><span data-stu-id="be4bf-375">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="be4bf-376">インデックスのデータベース名は、IndexBuilder.HasName を使用して構成されました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-376">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-377">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-377">New behavior</span></span>

<span data-ttu-id="be4bf-378">同じセットまたはプロパティに対して、複数のインデックスが許可されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-378">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="be4bf-379">これらのインデックスは、モデル内の名前で区別されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-379">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="be4bf-380">慣例により、モデル名はデータベース名として使用されます。ただし、HasDatabaseName を使用して個別に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-380">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-381">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-381">Why</span></span>

<span data-ttu-id="be4bf-382">Microsoft では将来的に、昇順と降順の両方のインデックス、または同じプロパティ セットに対して異なる照合順序を持つインデックスを有効にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-382">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="be4bf-383">この変更により、その方向に新たな一歩を踏み出すことができます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-383">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-384">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-384">Mitigations</span></span>

<span data-ttu-id="be4bf-385">以前に IndexBuilder.HasName を呼び出していたコードは、代わりに HasDatabaseName を呼び出すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-385">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="be4bf-386">EF Core バージョン 2.0.0 より前に生成された移行がプロジェクトに含まれている場合は、それらのファイルの警告を無視してもかまわず、`#pragma warning disable 612, 618` を追加して抑制することができます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-386">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="be4bf-387">リバース エンジニアリングされたモデルをスキャフォールディングするため、プルーラライザーが含まれるようになっています</span><span class="sxs-lookup"><span data-stu-id="be4bf-387">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="be4bf-388">問題 #11160 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-388">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-389">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-389">Old behavior</span></span>

<span data-ttu-id="be4bf-390">以前は、データベース スキーマのリバース エンジニアリングによって DbContext とエンティティ型をスキャフォールディングするときに、DbSet およびコレクション ナビゲーションの名前を複数化し、テーブル名を単数化するために、個別のプルーラライザー パッケージをインストールする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-390">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-391">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-391">New behavior</span></span>

<span data-ttu-id="be4bf-392">EF Core に、[Humanizer](https://github.com/Humanizr/Humanizer) ライブラリを使用するプルーラライザーが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-392">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="be4bf-393">これは、変数名を推奨するために Visual Studio で使用されるライブラリと同じものです。</span><span class="sxs-lookup"><span data-stu-id="be4bf-393">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-394">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-394">Why</span></span>

<span data-ttu-id="be4bf-395">コレクション プロパティに対して複数形の単語を使い、型および参照プロパティに対して単数形を使うのは、.NET では慣用的です。</span><span class="sxs-lookup"><span data-stu-id="be4bf-395">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-396">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-396">Mitigations</span></span>

<span data-ttu-id="be4bf-397">プルーラライザーを無効にするには、`dotnet ef dbcontext scaffold` で `--no-pluralize` オプションを使用するか、`Scaffold-DbContext` で `-NoPluralize` スイッチを使用します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-397">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="be4bf-398">スキップ ナビゲーションをサポートするため一部の API で INavigation が INavigationBase に置き換えられます</span><span class="sxs-lookup"><span data-stu-id="be4bf-398">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="be4bf-399">イシュー #2568 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-399">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="be4bf-400">以前の動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-400">Old behavior</span></span>

<span data-ttu-id="be4bf-401">5\.0 より前の EF Core では、`INavigation` インターフェイスによって表される 1 つの形式のナビゲーション プロパティのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-401">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="be4bf-402">新しい動作</span><span class="sxs-lookup"><span data-stu-id="be4bf-402">New behavior</span></span>

<span data-ttu-id="be4bf-403">EF Core 5.0 では、"スキップ ナビゲーション" を使用する多対多のリレーションシップが導入されています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-403">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="be4bf-404">これらは `ISkipNavigation` インターフェイスによって表され、`INavigation` のほとんどの機能は、共通の基底インターフェイスである `INavigationBase` に移されています。</span><span class="sxs-lookup"><span data-stu-id="be4bf-404">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="be4bf-405">理由</span><span class="sxs-lookup"><span data-stu-id="be4bf-405">Why</span></span>

<span data-ttu-id="be4bf-406">通常のナビゲーションとスキップ ナビゲーションの機能のほとんどは同じです。</span><span class="sxs-lookup"><span data-stu-id="be4bf-406">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="be4bf-407">ただし、関連する外部キーはリレーションシップのどちらの端にも直接は存在せず、結合エンティティ内にあるため、スキップ ナビゲーションでの FK へのリレーションシップは通常のナビゲーションとは異なります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-407">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="be4bf-408">軽減策</span><span class="sxs-lookup"><span data-stu-id="be4bf-408">Mitigations</span></span>

<span data-ttu-id="be4bf-409">多くの場合は、他の変更なしで新しい基底インターフェイスを使用するようにアプリケーションを切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-409">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="be4bf-410">ただし、外部キー プロパティへのアクセスにナビゲーションが使用されている場合は、アプリケーションのコードを、通常のナビゲーションのみに制限するか、通常とスキップの両方のナビゲーションで適切な処理を行うように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-410">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="be4bf-411">相関コレクションが含まれ、`Distinct` または `GroupBy` も使用されている一部のクエリが、サポートされなくなりました</span><span class="sxs-lookup"><span data-stu-id="be4bf-411">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="be4bf-412">イシュー #15873 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-412">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="be4bf-413">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="be4bf-413">**Old behavior**</span></span>

<span data-ttu-id="be4bf-414">以前は、相関コレクションに続いて `GroupBy` が含まれるクエリおよび `Distinct` を使用する一部のクエリは、実行が許可されていました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-414">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="be4bf-415">GroupBy の例:</span><span class="sxs-lookup"><span data-stu-id="be4bf-415">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="be4bf-416">`Distinct` の例 - 具体的には、内部コレクション プロジェクションに主キーが含まれていない `Distinct` クエリ:</span><span class="sxs-lookup"><span data-stu-id="be4bf-416">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="be4bf-417">これらのクエリは、内部コレクションに重複が含まれている場合は正しくない結果を返す可能性がありましたが、内部コレクション内のすべての要素が一意である場合は正しく動作しました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-417">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="be4bf-418">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="be4bf-418">**New behavior**</span></span>

<span data-ttu-id="be4bf-419">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-419">These queries are no longer supported.</span></span> <span data-ttu-id="be4bf-420">結果を正しく作成するための十分な情報がないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-420">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="be4bf-421">**理由**</span><span class="sxs-lookup"><span data-stu-id="be4bf-421">**Why**</span></span>

<span data-ttu-id="be4bf-422">相関コレクションのシナリオの場合、正しい親にコレクション エンティティを割り当てるには、エンティティの主キーがわかっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-422">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="be4bf-423">内部コレクションで `GroupBy` または `Distinct` が使用されていない場合は、足りない主キーをプロジェクションに単に追加できます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-423">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="be4bf-424">ただし、`GroupBy` と `Distinct` の場合は、`GroupBy` または `Distinct` の操作の結果が変わってしまうため実行できません。</span><span class="sxs-lookup"><span data-stu-id="be4bf-424">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="be4bf-425">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="be4bf-425">**Mitigations**</span></span>

<span data-ttu-id="be4bf-426">内部コレクションで `GroupBy` または `Distinct` 操作を使用しないようにクエリを書き直し、クライアントでこれらの操作を代わりに実行します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-426">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="be4bf-427">プロジェクションでのクエリ可能型のコレクションの使用はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="be4bf-427">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="be4bf-428">イシュー #16314 の追跡</span><span class="sxs-lookup"><span data-stu-id="be4bf-428">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="be4bf-429">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="be4bf-429">**Old behavior**</span></span>

<span data-ttu-id="be4bf-430">以前は、たとえば `List<T>` コンストラクターへの引数のような一部の場合には、プロジェクションの内部でクエリ可能型のコレクションを使用できました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-430">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="be4bf-431">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="be4bf-431">**New behavior**</span></span>

<span data-ttu-id="be4bf-432">これらのクエリはサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-432">These queries are no longer supported.</span></span> <span data-ttu-id="be4bf-433">クエリ可能型のオブジェクトを作成できないことが示され、これを修正する方法が提案されている、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-433">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="be4bf-434">**理由**</span><span class="sxs-lookup"><span data-stu-id="be4bf-434">**Why**</span></span>

<span data-ttu-id="be4bf-435">クエリ可能型のオブジェクトを具体化することはできないので、それらは代わりに `List<T>` 型を使用して自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="be4bf-435">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="be4bf-436">このため、型の不一致による例外が発生する場合がよくあります。これはわかりにくいものであり、ユーザーが驚くことがあります。</span><span class="sxs-lookup"><span data-stu-id="be4bf-436">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="be4bf-437">パターンを認識し、より意味のある例外をスローすることにしました。</span><span class="sxs-lookup"><span data-stu-id="be4bf-437">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="be4bf-438">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="be4bf-438">**Mitigations**</span></span>

<span data-ttu-id="be4bf-439">プロジェクション内でクエリ可能オブジェクトの後に `ToList()` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="be4bf-439">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
