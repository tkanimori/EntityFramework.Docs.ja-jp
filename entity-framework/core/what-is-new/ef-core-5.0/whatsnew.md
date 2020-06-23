---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418943"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="d76a4-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="d76a4-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="d76a4-104">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="d76a4-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="d76a4-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="d76a4-106">このページには [EF Core 5.0 のプラン](plan.md)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="d76a4-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="d76a4-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="d76a4-108">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="d76a4-109">Preview 5</span><span class="sxs-lookup"><span data-stu-id="d76a4-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="d76a4-110">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="d76a4-110">Database collations</span></span>

<span data-ttu-id="d76a4-111">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="d76a4-112">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="d76a4-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="d76a4-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d76a4-114">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="d76a4-115">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="d76a4-116">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d76a4-117">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="d76a4-118">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="d76a4-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="d76a4-119">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="d76a4-120">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="d76a4-121">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="d76a4-122">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="d76a4-123">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="d76a4-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="d76a4-124">引数は、コマンド ラインから [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="d76a4-125">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="d76a4-126">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="d76a4-127">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="d76a4-128">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="d76a4-129">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="d76a4-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="d76a4-130">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="d76a4-131">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d76a4-132">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d76a4-133">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="d76a4-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="d76a4-134">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="d76a4-135">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="d76a4-136">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="d76a4-137">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="d76a4-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="d76a4-138">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="d76a4-139">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="d76a4-140">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="d76a4-141">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="d76a4-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="d76a4-143">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="d76a4-143">SQLite computed columns</span></span>

<span data-ttu-id="d76a4-144">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="d76a4-145">Preview 4</span><span class="sxs-lookup"><span data-stu-id="d76a4-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="d76a4-146">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="d76a4-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="d76a4-147">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="d76a4-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="d76a4-149">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="d76a4-150">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="d76a4-151">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="d76a4-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="d76a4-152">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="d76a4-153">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="d76a4-154">Preview 3</span><span class="sxs-lookup"><span data-stu-id="d76a4-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="d76a4-155">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="d76a4-155">Filtered Include</span></span>

<span data-ttu-id="d76a4-156">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="d76a4-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="d76a4-158">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="d76a4-159">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="d76a4-160">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="d76a4-161">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="d76a4-162">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="d76a4-163">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="d76a4-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="d76a4-164">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="d76a4-165">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="d76a4-166">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="d76a4-167">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="d76a4-168">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="d76a4-169">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="d76a4-170">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="d76a4-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="d76a4-171">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="d76a4-172">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="d76a4-173">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="d76a4-174">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="d76a4-175">詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d76a4-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="d76a4-176">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="d76a4-177">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="d76a4-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="d76a4-178">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="d76a4-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="d76a4-179">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="d76a4-180">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="d76a4-181">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="d76a4-182">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="d76a4-183">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="d76a4-183">Cosmos partition keys</span></span>

<span data-ttu-id="d76a4-184">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="d76a4-185">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="d76a4-186">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="d76a4-187">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="d76a4-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="d76a4-188">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="d76a4-189">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="d76a4-190">Preview 2</span><span class="sxs-lookup"><span data-stu-id="d76a4-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="d76a4-191">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="d76a4-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="d76a4-192">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="d76a4-193">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="d76a4-194">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-194">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="d76a4-195">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="d76a4-196">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="d76a4-196">Complete discriminator mapping</span></span>

<span data-ttu-id="d76a4-197">EF Core では、[継承階層の TPH マッピング](/ef/core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="d76a4-198">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="d76a4-199">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="d76a4-200">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="d76a4-201">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="d76a4-202">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="d76a4-203">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="d76a4-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="d76a4-204">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="d76a4-205">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="d76a4-206">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="d76a4-207">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="d76a4-208">Preview 1</span><span class="sxs-lookup"><span data-stu-id="d76a4-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="d76a4-209">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="d76a4-209">Simple logging</span></span>

<span data-ttu-id="d76a4-210">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="d76a4-211">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="d76a4-212">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d76a4-213">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="d76a4-214">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="d76a4-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="d76a4-215">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="d76a4-216">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="d76a4-217">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="d76a4-218">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="d76a4-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="d76a4-219">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="d76a4-220">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="d76a4-221">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="d76a4-222">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="d76a4-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="d76a4-223">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="d76a4-224">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="d76a4-225">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="d76a4-226">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="d76a4-227">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="d76a4-227">Change-tracking proxies</span></span>

<span data-ttu-id="d76a4-228">EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="d76a4-229">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="d76a4-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="d76a4-230">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="d76a4-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="d76a4-231">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="d76a4-232">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="d76a4-232">Enhanced debug views</span></span>

<span data-ttu-id="d76a4-233">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="d76a4-234">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="d76a4-235">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="d76a4-236">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="d76a4-237">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="d76a4-238">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="d76a4-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="d76a4-239">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="d76a4-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="d76a4-240">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="d76a4-241">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="d76a4-242">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="d76a4-243">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="d76a4-244">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="d76a4-244">Indexer properties</span></span>

<span data-ttu-id="d76a4-245">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="d76a4-246">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="d76a4-247">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="d76a4-248">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="d76a4-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="d76a4-249">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="d76a4-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="d76a4-251">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="d76a4-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="d76a4-252">IsRelational</span></span>

<span data-ttu-id="d76a4-253">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="d76a4-254">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="d76a4-255">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="d76a4-256">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="d76a4-257">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="d76a4-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="d76a4-258">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="d76a4-259">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="d76a4-260">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](https://docs.microsoft.com/ef/core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="d76a4-261">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="d76a4-262">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d76a4-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="d76a4-263">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="d76a4-264">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="d76a4-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="d76a4-265">DateDiffWeek</span></span>
* <span data-ttu-id="d76a4-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="d76a4-266">DateFromParts</span></span>

<span data-ttu-id="d76a4-267">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="d76a4-268">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="d76a4-269">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d76a4-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="d76a4-270">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="d76a4-271">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d76a4-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d76a4-272">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="d76a4-273">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d76a4-273">Query translation for Reverse</span></span>

<span data-ttu-id="d76a4-274">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="d76a4-275">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d76a4-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="d76a4-276">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="d76a4-277">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d76a4-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="d76a4-278">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="d76a4-279">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="d76a4-280">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d76a4-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="d76a4-281">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d76a4-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="d76a4-282">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d76a4-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
