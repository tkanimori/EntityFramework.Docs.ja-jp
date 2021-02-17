---
title: 生成された値-EF Core
description: Entity Framework Core を使用する場合のプロパティの値生成の構成方法
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: a9e43f3b755bf028bc76581135988e831a42d0d1
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543342"
---
# <a name="generated-values"></a><span data-ttu-id="11cd2-103">生成された値</span><span class="sxs-lookup"><span data-stu-id="11cd2-103">Generated Values</span></span>

<span data-ttu-id="11cd2-104">データベース列の値は、さまざまな方法で生成できます。主キー列は、自動インクリメントの整数、他の列には既定値または計算値などがあります。このページでは、EF Core で構成値を生成するためのさまざまなパターンについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="11cd2-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="11cd2-105">既定値</span><span class="sxs-lookup"><span data-stu-id="11cd2-105">Default values</span></span>

<span data-ttu-id="11cd2-106">リレーショナルデータベースでは、既定値を使用して列を構成できます。その列の値を指定せずに行が挿入されると、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="11cd2-107">プロパティの既定値を構成できます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="11cd2-108">既定値の計算に使用される SQL フラグメントを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="11cd2-109">計算列</span><span class="sxs-lookup"><span data-stu-id="11cd2-109">Computed columns</span></span>

<span data-ttu-id="11cd2-110">ほとんどのリレーショナルデータベースでは、データベース内の値を計算するように列を構成できます。通常は、他の列を参照する式を使用します。</span><span class="sxs-lookup"><span data-stu-id="11cd2-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="11cd2-111">上の例では、データベースからフェッチされるたびに値が計算される、 *仮想* 計算列が作成されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="11cd2-112">計算列を *格納* するように指定することもできます *。これ* は、計算列が行のすべての更新で計算され、通常の列と共にディスクに格納されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="11cd2-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="11cd2-113">格納されている計算列の作成のサポートが EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="11cd2-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="11cd2-114">主キー</span><span class="sxs-lookup"><span data-stu-id="11cd2-114">Primary keys</span></span>

<span data-ttu-id="11cd2-115">慣例として、short、int、long、または Guid 型の非複合主キーは、アプリケーションによって値が提供されない場合に、挿入されたエンティティに対して値が生成されるように設定されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="11cd2-116">通常、データベースプロバイダーは必要な構成を行います。たとえば、SQL Server の数値主キーは、ID 列として自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="11cd2-117">詳細については、 [キーに関するドキュメントを参照してください](xref:core/modeling/keys)。</span><span class="sxs-lookup"><span data-stu-id="11cd2-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="11cd2-118">値の生成を明示的に構成する</span><span class="sxs-lookup"><span data-stu-id="11cd2-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="11cd2-119">上で説明したように、主キーの値生成は自動的に設定されていますが、キー以外のプロパティに対しても同様の処理を行うことができ EF Core。</span><span class="sxs-lookup"><span data-stu-id="11cd2-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="11cd2-120">挿入されたエンティティに対して値が生成されるようにプロパティを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="11cd2-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="11cd2-121">データの注釈</span><span class="sxs-lookup"><span data-stu-id="11cd2-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="11cd2-122">Fluent API</span><span class="sxs-lookup"><span data-stu-id="11cd2-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="11cd2-123">同様に、追加または更新時に値が生成されるようにプロパティを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="11cd2-124">データの注釈</span><span class="sxs-lookup"><span data-stu-id="11cd2-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="11cd2-125">Fluent API</span><span class="sxs-lookup"><span data-stu-id="11cd2-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> <span data-ttu-id="11cd2-126">既定値または計算列とは異なり、ここでは値の生成 *方法* を指定していません。これは、使用されているデータベースプロバイダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-126">Unlike with default values or computed columns, we are not specifying *how* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="11cd2-127">データベースプロバイダーでは、一部のプロパティの型に対して自動的に値の生成が設定される場合がありますが、その他の場合は、値の生成方法を手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-127">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="11cd2-128">たとえば、SQL Server で、GUID プロパティが追加時に生成された値として構成されている場合、プロバイダーは、最適なシーケンシャル GUID 値を生成するアルゴリズムを使用して、値生成クライアント側を自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="11cd2-128">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="11cd2-129">ただし、DateTime プロパティにを指定しても `ValueGeneratedOnAdd()` 効果はありません ([datetime 値の生成については、以下のセクションを参照して](#datetime-value-generation)ください)。</span><span class="sxs-lookup"><span data-stu-id="11cd2-129">However, specifying `ValueGeneratedOnAdd()` on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="11cd2-130">同様に、add または update で生成されるように構成され、同時実行トークンとしてマークされている byte [] プロパティは、rowversion データ型を使用して設定されるので、データベースで値が自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-130">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="11cd2-131">ただし、を指定しても何も `ValueGeneratedOnAddOrUpdate()` 起こりません。</span><span class="sxs-lookup"><span data-stu-id="11cd2-131">However, specifying `ValueGeneratedOnAddOrUpdate()` will again have no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="11cd2-132">使用するデータベースプロバイダーによっては、値が EF またはデータベースでクライアント側で生成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-132">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="11cd2-133">値がデータベースによって生成された場合、エンティティをコンテキストに追加するときに EF によって一時的な値が割り当てられることがあります。この一時値は、の間にデータベースによって生成された値に置き換えられ `SaveChanges()` ます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-133">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="11cd2-134">詳細については、 [一時値に関するドキュメントを参照してください](xref:core/change-tracking/explicit-tracking#temporary-values)。</span><span class="sxs-lookup"><span data-stu-id="11cd2-134">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="11cd2-135">日付/時刻値の生成</span><span class="sxs-lookup"><span data-stu-id="11cd2-135">Date/time value generation</span></span>

<span data-ttu-id="11cd2-136">一般的な要求では、列が最初に挿入された日時 (追加時に値が生成されたとき)、または最終更新日 (追加または更新時に生成された値) の日付/時刻を含むデータベース列が必要です。</span><span class="sxs-lookup"><span data-stu-id="11cd2-136">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="11cd2-137">これを行うにはさまざまな方法がありますが、EF Core プロバイダーは、通常、日付/時刻列に対して自動的に値の生成を設定しません。これを自分で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-137">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="11cd2-138">作成タイムスタンプ</span><span class="sxs-lookup"><span data-stu-id="11cd2-138">Creation timestamp</span></span>

<span data-ttu-id="11cd2-139">通常、行の作成タイムスタンプを持つように日付/時刻列を構成する場合は、適切な SQL 関数を使用して既定値を構成することが重要です。</span><span class="sxs-lookup"><span data-stu-id="11cd2-139">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="11cd2-140">たとえば SQL Server では、次のものを使用できます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-140">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="11cd2-141">必要に応じて、適切な関数を選択してください (例: `GETDATE()` vs. `GETUTCDATE()` )。</span><span class="sxs-lookup"><span data-stu-id="11cd2-141">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="11cd2-142">タイムスタンプの更新</span><span class="sxs-lookup"><span data-stu-id="11cd2-142">Update timestamp</span></span>

<span data-ttu-id="11cd2-143">格納されている計算列は最後に更新されたタイムスタンプを管理するための優れたソリューションであるように見えますが、データベースでは通常、計算列になどの関数を指定することはできません `GETDATE()` 。</span><span class="sxs-lookup"><span data-stu-id="11cd2-143">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="11cd2-144">別の方法として、データベーストリガーを設定して同じ効果を得ることもできます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-144">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

<span data-ttu-id="11cd2-145">トリガーの作成の詳細については、 [「移行における生の SQL の使用に関するドキュメント」を参照してください](xref:core/managing-schemas/migrations/managing#adding-raw-sql)。</span><span class="sxs-lookup"><span data-stu-id="11cd2-145">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="11cd2-146">オーバーライド (値の生成を)</span><span class="sxs-lookup"><span data-stu-id="11cd2-146">Overriding value generation</span></span>

<span data-ttu-id="11cd2-147">プロパティは値の生成用に構成されていますが、多くの場合、その値を明示的に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-147">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="11cd2-148">実際に動作するかどうかは、構成されている特定の値生成メカニズムによって異なります。列の既定値を使用するのではなく、明示的な値を指定することもできますが、計算列で同じことを行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="11cd2-148">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="11cd2-149">明示的な値を使用して値の生成をオーバーライドするには、そのプロパティの型の CLR 既定値ではない任意の値 (for、for、for、など) にプロパティを設定するだけです `null` `string` `0` `int` `Guid.Empty` `Guid` 。</span><span class="sxs-lookup"><span data-stu-id="11cd2-149">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="11cd2-150">既定では、明示的な値を SQL Server ID に挿入しようとすると失敗します。 [回避策については、次のドキュメントを参照してください](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)。</span><span class="sxs-lookup"><span data-stu-id="11cd2-150">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="11cd2-151">追加または更新時に生成される値として構成されたプロパティの値を明示的に指定するには、次のようにプロパティを構成する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-151">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="11cd2-152">値の生成なし</span><span class="sxs-lookup"><span data-stu-id="11cd2-152">No value generation</span></span>

<span data-ttu-id="11cd2-153">前述のような特定のシナリオとは別に、プロパティには、通常、値の生成が構成されていません。つまり、データベースに保存される値を常に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-153">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="11cd2-154">この値は、コンテキストに追加する前に、新しいエンティティに割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-154">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="11cd2-155">ただし、場合によっては、規約によって設定された値の生成を無効にすることが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="11cd2-155">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="11cd2-156">たとえば、int 型の主キーは、通常、追加時の値 (たとえば、SQL Server の id 列) として暗黙的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-156">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="11cd2-157">これは、次の方法で無効にできます。</span><span class="sxs-lookup"><span data-stu-id="11cd2-157">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="11cd2-158">データの注釈</span><span class="sxs-lookup"><span data-stu-id="11cd2-158">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="11cd2-159">Fluent API</span><span class="sxs-lookup"><span data-stu-id="11cd2-159">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
