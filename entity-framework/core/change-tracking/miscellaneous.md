---
title: その他の Change Tracking 機能-EF Core
description: EF Core の変更の追跡に関連するその他の機能とシナリオ
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: db1e32948b2a60ad1b85e300bbbccd54d49a84e5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129617"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="a1177-103">その他の Change Tracking 機能</span><span class="sxs-lookup"><span data-stu-id="a1177-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="a1177-104">このドキュメントでは、変更の追跡に関連するその他の機能とシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a1177-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="a1177-105">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="a1177-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="a1177-106">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1177-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="a1177-107">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-verses-addasync"></a><span data-ttu-id="a1177-108">定型非同期の追加</span><span class="sxs-lookup"><span data-stu-id="a1177-108">Add verses AddAsync</span></span>

<span data-ttu-id="a1177-109">Entity Framework Core (EF Core) は、そのメソッドを使用すると、データベースとの対話が発生する可能性がある場合に、非同期メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="a1177-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="a1177-110">また、高パフォーマンスの非同期アクセスをサポートしていないデータベースを使用する場合のオーバーヘッドを避けるために、同期メソッドも用意されています。</span><span class="sxs-lookup"><span data-stu-id="a1177-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="a1177-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType>通常はデータベースにアクセスしません。これらのメソッドは、エンティティの追跡を開始するだけであるためです。</span><span class="sxs-lookup"><span data-stu-id="a1177-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="a1177-112">ただし、値生成の形式によっては、キー値を生成するためにデータベースにアクセスする _場合があり_ ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="a1177-113">これを行う唯一の値ジェネレーターは、EF Core に付属してい <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="a1177-114">このジェネレーターの使用は一般的ではありません。既定では構成されません。</span><span class="sxs-lookup"><span data-stu-id="a1177-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="a1177-115">これは、ほとんどのアプリケーションではなく、を使用する必要があることを意味し `Add` `AddAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="a1177-116">、、などのその他の同様のメソッド `Update` に `Attach` `Remove` は、新しいキー値を生成しないため、データベースにアクセスする必要がないため、非同期のオーバーロードはありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="a1177-117">AddRange、UpdateRange、AttachRange、および RemoveRange 種類</span><span class="sxs-lookup"><span data-stu-id="a1177-117">AddRange, UpdateRange, AttachRange, and RemoveRange</span></span>

<span data-ttu-id="a1177-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> とは <xref:Microsoft.EntityFrameworkCore.DbContext> `Add` 、 `Update` `Attach` `Remove` 1 回の呼び出しで複数のインスタンスを受け入れる、、、およびの代替バージョンを提供します。</span><span class="sxs-lookup"><span data-stu-id="a1177-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="a1177-119">これらのメソッドは `AddRange` 、それぞれ、、、 `UpdateRange` およびと呼ばれ `AttachRange` `RemoveRange` ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-119">These methods are called `AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange` respectively.</span></span>

<span data-ttu-id="a1177-120">これらのメソッドは便利な方法として提供されています。</span><span class="sxs-lookup"><span data-stu-id="a1177-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="a1177-121">"Range" メソッドを使用すると、同等の非範囲メソッドを複数回呼び出した場合と同じ機能が得られます。</span><span class="sxs-lookup"><span data-stu-id="a1177-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="a1177-122">2つの方法には、パフォーマンスに大きな違いはありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="a1177-123">これは EF6 とは異なります。この場合、AddRange とは両方とも自動的に検出された変更として呼び出されますが、Add を複数回呼び出すと、検出された変更が1回ではなく複数回呼び出される原因になります。</span><span class="sxs-lookup"><span data-stu-id="a1177-123">This is different from EF6, where AddRange and Add both automatically called DetectChanges, but calling Add multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="a1177-124">これにより、EF6 での AddRange の効率が向上しました。</span><span class="sxs-lookup"><span data-stu-id="a1177-124">This made AddRange more efficient in EF6.</span></span> <span data-ttu-id="a1177-125">EF Core では、どちらのメソッドも、検出された変更を自動的に呼び出すことはありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-125">In EF Core, neither of these methods automatically call DetectChanges.</span></span>

## <a name="dbcontext-verses-dbset-methods"></a><span data-ttu-id="a1177-126">DbContext と Dbcontext メソッド</span><span class="sxs-lookup"><span data-stu-id="a1177-126">DbContext verses DbSet methods</span></span>

<span data-ttu-id="a1177-127">、、、などの多くのメソッドには、 `Add` `Update` `Attach` `Remove` との両方に実装があり <xref:Microsoft.EntityFrameworkCore.DbSet%601> <xref:Microsoft.EntityFrameworkCore.DbContext> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="a1177-128">これらのメソッドの動作は、通常のエンティティ型 _とまったく同じ_ です。</span><span class="sxs-lookup"><span data-stu-id="a1177-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="a1177-129">これは、エンティティの CLR 型が、EF Core モデルの1つのエンティティ型にのみマップされるためです。</span><span class="sxs-lookup"><span data-stu-id="a1177-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="a1177-130">したがって、CLR 型は、エンティティがモデルに適合する場所を完全に定義するので、使用する DbSet を暗黙的に決定できます。</span><span class="sxs-lookup"><span data-stu-id="a1177-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="a1177-131">この規則の例外は、EF Core 5.0 で導入された共有型のエンティティ型を使用する場合です。これは、主に多対多の結合エンティティに対して行われます。</span><span class="sxs-lookup"><span data-stu-id="a1177-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="a1177-132">共有型のエンティティ型を使用する場合は、使用する EF Core モデル型に対して、最初に DbSet を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="a1177-133">、、、などのメソッドは、使用されている `Add` `Update` `Attach` `Remove` EF Core モデル型を明確にすることなく、dbset で使用できます。</span><span class="sxs-lookup"><span data-stu-id="a1177-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="a1177-134">共有型のエンティティ型は、多対多リレーションシップの結合エンティティに対して既定で使用されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="a1177-135">また、共有型のエンティティ型は、多対多リレーションシップで使用するように明示的に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="a1177-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="a1177-136">たとえば、次のコードは、 `Dictionary<string, int>` 結合エンティティ型としてを構成します。</span><span class="sxs-lookup"><span data-stu-id="a1177-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="a1177-137">[外部キーとナビゲーションを変更すると](xref:core/change-tracking/relationship-changes) 、新しい結合エンティティインスタンスを追跡することによって、2つのエンティティを関連付ける方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="a1177-138">次のコードは、 `Dictionary<string, int>` 結合エンティティに使用される共有型のエンティティ型に対してこれを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1177-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_verses_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_verses_DbSet_methods_1)]

<span data-ttu-id="a1177-139"><xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType>は、エンティティ型の DbSet を作成するために使用されることに注意 `PostTag` してください。</span><span class="sxs-lookup"><span data-stu-id="a1177-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="a1177-140">この DbSet を使用し `Add` て、新しい結合エンティティインスタンスでを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a1177-141">規則に従ってエンティティ型を結合するために使用される CLR 型は、パフォーマンスを向上させるために将来のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="a1177-142">上記のコードでのの実行として明示的に構成されている場合を除き、特定の結合エンティティ型に依存しないでください `Dictionary<string, int>` 。</span><span class="sxs-lookup"><span data-stu-id="a1177-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-verses-field-access"></a><span data-ttu-id="a1177-143">プロパティとフィールドのアクセス</span><span class="sxs-lookup"><span data-stu-id="a1177-143">Property verses field access</span></span>

<span data-ttu-id="a1177-144">EF Core 3.0 以降では、エンティティプロパティへのアクセスには、既定でプロパティのバッキングフィールドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="a1177-145">これは効率的であり、プロパティの getter と setter を呼び出すことによる副作用のトリガーを回避します。</span><span class="sxs-lookup"><span data-stu-id="a1177-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="a1177-146">たとえば、遅延読み込みによって無限ループがトリガーされるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="a1177-147">モデルでのバッキングフィールドの構成の詳細については、「 [バッキングフィールド](xref:core/modeling/backing-field) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1177-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="a1177-148">場合によっては、プロパティ値を変更するときに、EF Core が副作用を生成することが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="a1177-149">たとえば、エンティティにデータをバインドするときに、プロパティを設定すると、U.I. への通知が生成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="a1177-150">これは、フィールドを直接設定するときには発生しません。</span><span class="sxs-lookup"><span data-stu-id="a1177-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="a1177-151">これは、のを変更することで実現でき <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="a1177-152">を使用したモデル内のすべてのエンティティ型 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="a1177-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="a1177-153">を使用した特定のエンティティ型のすべてのプロパティとナビゲーション <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="a1177-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="a1177-154">を使用する特定のプロパティ <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="a1177-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="a1177-155">を使用した特定のナビゲーション <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="a1177-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="a1177-156">プロパティアクセスモード `Field` と `PreferField` により、EF Core は、バッキングフィールドを介してプロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="a1177-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="a1177-157">同様 `Property` に、と `PreferProperty` は、getter および setter を介して、EF Core がプロパティ値にアクセスすることになります。</span><span class="sxs-lookup"><span data-stu-id="a1177-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="a1177-158">`Field`または `Property` が使用されていて、EF Core がフィールドまたはプロパティの getter/setter を使用して値にアクセスできない場合、EF Core は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a1177-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="a1177-159">これにより、常にフィールドまたはプロパティへのアクセスが使用されていると考えられる場合に、EF Core が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="a1177-160">一方、 `PreferField` `PreferProperty` 優先アクセスを使用できない場合、モードとモードはそれぞれ、プロパティまたはバッキングフィールドを使用するように切り替えられます。</span><span class="sxs-lookup"><span data-stu-id="a1177-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="a1177-161">`PreferField` は EF Core 3.0 以降の既定値です。</span><span class="sxs-lookup"><span data-stu-id="a1177-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="a1177-162">つまり、EF Core では、可能な場合は常にフィールドが使用されますが、getter または setter を使用してプロパティにアクセスする必要がある場合は失敗しません。</span><span class="sxs-lookup"><span data-stu-id="a1177-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="a1177-163">`FieldDuringConstruction` また、 `PreferFieldDuringConstruction` _エンティティインスタンスの作成時にのみ_ バッキングフィールドを使用するように EF Core を構成します。</span><span class="sxs-lookup"><span data-stu-id="a1177-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="a1177-164">これにより、getter と setter の副作用なしでクエリを実行できるようになります。 EF Core によって後で変更されると、これらの副作用が発生します。</span><span class="sxs-lookup"><span data-stu-id="a1177-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="a1177-165">`PreferFieldDuringConstruction` は EF Core 3.0 より前の既定値でした。</span><span class="sxs-lookup"><span data-stu-id="a1177-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="a1177-166">次の表では、さまざまなプロパティアクセスモードについてまとめています。</span><span class="sxs-lookup"><span data-stu-id="a1177-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="a1177-167">PropertyAccessMode</span><span class="sxs-lookup"><span data-stu-id="a1177-167">PropertyAccessMode</span></span>              | <span data-ttu-id="a1177-168">順位</span><span class="sxs-lookup"><span data-stu-id="a1177-168">Preference</span></span> | <span data-ttu-id="a1177-169">エンティティ作成の基本設定</span><span class="sxs-lookup"><span data-stu-id="a1177-169">Preference creating entities</span></span> | <span data-ttu-id="a1177-170">フォールバック</span><span class="sxs-lookup"><span data-stu-id="a1177-170">Fallback</span></span> | <span data-ttu-id="a1177-171">フォールバック (エンティティを作成する)</span><span class="sxs-lookup"><span data-stu-id="a1177-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="a1177-172">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-172">Field</span></span>      | <span data-ttu-id="a1177-173">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-173">Field</span></span>                        | <span data-ttu-id="a1177-174">スロー</span><span class="sxs-lookup"><span data-stu-id="a1177-174">Throws</span></span>   | <span data-ttu-id="a1177-175">スロー</span><span class="sxs-lookup"><span data-stu-id="a1177-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="a1177-176">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-176">Property</span></span>   | <span data-ttu-id="a1177-177">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-177">Property</span></span>                     | <span data-ttu-id="a1177-178">スロー</span><span class="sxs-lookup"><span data-stu-id="a1177-178">Throws</span></span>   | <span data-ttu-id="a1177-179">スロー</span><span class="sxs-lookup"><span data-stu-id="a1177-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="a1177-180">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-180">Field</span></span>      | <span data-ttu-id="a1177-181">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-181">Field</span></span>                        | <span data-ttu-id="a1177-182">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-182">Property</span></span> | <span data-ttu-id="a1177-183">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="a1177-184">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-184">Property</span></span>   | <span data-ttu-id="a1177-185">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-185">Property</span></span>                     | <span data-ttu-id="a1177-186">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-186">Field</span></span>    | <span data-ttu-id="a1177-187">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="a1177-188">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-188">Property</span></span>   | <span data-ttu-id="a1177-189">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-189">Field</span></span>                        | <span data-ttu-id="a1177-190">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-190">Field</span></span>    | <span data-ttu-id="a1177-191">スロー</span><span class="sxs-lookup"><span data-stu-id="a1177-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="a1177-192">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-192">Property</span></span>   | <span data-ttu-id="a1177-193">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-193">Field</span></span>                        | <span data-ttu-id="a1177-194">フィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-194">Field</span></span>    | <span data-ttu-id="a1177-195">プロパティ</span><span class="sxs-lookup"><span data-stu-id="a1177-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="a1177-196">一時的な値</span><span class="sxs-lookup"><span data-stu-id="a1177-196">Temporary values</span></span>

<span data-ttu-id="a1177-197">EF Core は、SaveChanges が呼び出されたときにデータベースによって生成される実際のキー値を持つ新しいエンティティを追跡するときに、一時的なキー値を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1177-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="a1177-198">これらの一時的な値の使用方法の概要については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1177-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="a1177-199">一時的な値へのアクセス</span><span class="sxs-lookup"><span data-stu-id="a1177-199">Accessing temporary values</span></span>

<span data-ttu-id="a1177-200">EF Core 3.0 以降では、一時的な値は変更トラッカーに格納され、エンティティインスタンスに直接設定されることはありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="a1177-201">ただし、これらの一時値は、追跡対象のエンティティに [アクセス](xref:core/change-tracking/entity-entries)するためのさまざまなメカニズムを使用する場合に公開 _され_ ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="a1177-202">たとえば、次のコードはを使用して一時的な値にアクセスし <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="a1177-203">このコードからの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a1177-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="a1177-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> を使用すると、一時的な値を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a1177-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="a1177-205">一時値の操作</span><span class="sxs-lookup"><span data-stu-id="a1177-205">Manipulating temporary values</span></span>

<span data-ttu-id="a1177-206">一時的な値を明示的に使用すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="a1177-207">たとえば、新しいエンティティのコレクションを web クライアント上に作成し、サーバーにシリアル化して戻すことができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="a1177-208">外部キーの値は、これらのエンティティ間のリレーションシップを設定するための1つの方法です。</span><span class="sxs-lookup"><span data-stu-id="a1177-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="a1177-209">次のコードでは、この方法を使用して、新しいエンティティのグラフを外部キーで関連付け、SaveChanges が呼び出されたときに実際のキー値を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="a1177-210">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a1177-210">Notice that:</span></span>

- <span data-ttu-id="a1177-211">負の数値は、一時キーの値として使用されます。これは必須ではありませんが、キーの競合を防ぐための一般的な規則です。</span><span class="sxs-lookup"><span data-stu-id="a1177-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="a1177-212">`Post.BlogId`FK プロパティには、関連付けられているブログの PK と同じ負の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="a1177-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="a1177-213">各エンティティが追跡された後に、PK 値が一時的としてマークされ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="a1177-214">これは、アプリケーションによって指定されたキー値が実際のキー値であると見なされるために必要です。</span><span class="sxs-lookup"><span data-stu-id="a1177-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="a1177-215">SaveChanges を呼び出す前に [change tracker デバッグビューを確認](xref:core/change-tracking/debug-views) すると、PK 値が一時的としてマークされ、投稿が正しいブログに関連付けられていることがわかります。これには、ナビゲーションの修正が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a1177-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="a1177-216">を呼び出した後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 、これらの一時値は、データベースによって生成される実際の値に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="a1177-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="a1177-217">既定値の使用</span><span class="sxs-lookup"><span data-stu-id="a1177-217">Working with default values</span></span>

<span data-ttu-id="a1177-218">EF Core を指定すると、プロパティは、の呼び出し時にデータベースから既定値を取得でき <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="a1177-219">生成されたキー値と同様に、値が明示的に設定されていない場合、EF Core はデータベースからの既定値のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="a1177-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="a1177-220">たとえば、次のエンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a1177-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="a1177-221">`ValidFrom`プロパティは、データベースから既定値を取得するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a1177-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="a1177-222">この型のエンティティを挿入すると、明示的な値が設定されていない限り、EF Core によってデータベースで値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="a1177-223">例:</span><span class="sxs-lookup"><span data-stu-id="a1177-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="a1177-224">[変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views)を見ると、最初のトークンが `ValidFrom` データベースによって生成され、2番目のトークンが値を明示的に設定したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="a1177-225">データベースの既定値を使用するには、データベース列に既定値制約が構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="a1177-226">これは、またはを使用しているときに EF Core 移行によって自動的に行われ <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="a1177-227">EF Core 移行を使用しない場合は、他の方法で列に default 制約を作成するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="a1177-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="a1177-228">Null 値を許容するプロパティの使用</span><span class="sxs-lookup"><span data-stu-id="a1177-228">Using nullable properties</span></span>

<span data-ttu-id="a1177-229">EF Core は、プロパティ値をその型の CLR の既定値と比較することによって、プロパティが設定されているかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="a1177-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="a1177-230">これはほとんどの場合に適していますが、CLR default をデータベースに明示的に挿入することはできません。</span><span class="sxs-lookup"><span data-stu-id="a1177-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="a1177-231">たとえば、次のように整数のプロパティを持つエンティティについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a1177-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="a1177-232">このプロパティは、データベースの既定値-1 を持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a1177-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="a1177-233">その目的は、明示的な値が設定されていない場合に、既定の-1 が使用されることです。</span><span class="sxs-lookup"><span data-stu-id="a1177-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="a1177-234">ただし、値を 0 (整数の CLR の既定値) に設定しても、値が設定されていないことを EF Core すると区別できないため、このプロパティに0を挿入することはできません。</span><span class="sxs-lookup"><span data-stu-id="a1177-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="a1177-235">例:</span><span class="sxs-lookup"><span data-stu-id="a1177-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="a1177-236">`Count`が明示的に0に設定されているインスタンスは、引き続きデータベースから既定値を取得することに注意してください。これは、意図したものではありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="a1177-237">これに対処する簡単な方法は、プロパティを null 許容にすることです `Count` 。</span><span class="sxs-lookup"><span data-stu-id="a1177-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="a1177-238">これにより、CLR の既定値は0ではなく null になります。これは、明示的に設定したときに0が挿入されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a1177-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="a1177-239">Null 許容のバッキングフィールドの使用</span><span class="sxs-lookup"><span data-stu-id="a1177-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="a1177-240">この null 許容のバッキングフィールドパターンは EF Core 5.0 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a1177-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="a1177-241">このプロパティを null 許容にすると、ドメインモデルで概念的に null 値が許容されないことに問題があります。</span><span class="sxs-lookup"><span data-stu-id="a1177-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="a1177-242">このため、プロパティを強制的に nullable にすると、モデルは侵害されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="a1177-243">EF Core 5.0 以降では、プロパティを null 非許容にすることができます。これにより、バッキングフィールドだけを null 許容にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a1177-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="a1177-244">例:</span><span class="sxs-lookup"><span data-stu-id="a1177-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="a1177-245">これにより、プロパティが明示的に0に設定されている場合に CLR の既定値 (0) を挿入できるようになります。一方、ドメインモデルでは、プロパティを null 許容として公開する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a1177-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="a1177-246">例:</span><span class="sxs-lookup"><span data-stu-id="a1177-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="a1177-247">Bool プロパティの null 許容のバッキングフィールド</span><span class="sxs-lookup"><span data-stu-id="a1177-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="a1177-248">このパターンは、ブール型のプロパティをストアで生成された既定値と共に使用する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="a1177-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="a1177-249">の CLR の既定値 `bool` は "false" であるため、通常のパターンを使用して "false" を明示的に挿入することはできません。</span><span class="sxs-lookup"><span data-stu-id="a1177-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="a1177-250">たとえば、エンティティ型について考えてみ `User` ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="a1177-251">`IsAuthorized`プロパティは、データベースの既定値 "true" を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1177-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="a1177-252">プロパティは、 `IsAuthorized` 挿入前に明示的に "true" または "false" に設定できます。また、データベースの既定値をそのままにしておくこともできます。</span><span class="sxs-lookup"><span data-stu-id="a1177-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="a1177-253">SQLite を使用しているときの SaveChanges からの出力は、データベースの既定値が Mac で使用されていることを示していますが、Alice と Baxter には明示的な値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="a1177-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="a1177-254">スキーマの既定値のみ</span><span class="sxs-lookup"><span data-stu-id="a1177-254">Schema defaults only</span></span>

<span data-ttu-id="a1177-255">場合によっては、これらの値を挿入に使用しなくて EF Core も、EF Core の移行によって作成されたデータベーススキーマに既定値を設定すると便利です。</span><span class="sxs-lookup"><span data-stu-id="a1177-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="a1177-256">これは、次のようにプロパティを構成することによって実現でき <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="a1177-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
