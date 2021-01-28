---
title: 値の変換-EF Core
description: Entity Framework Core モデルでの値コンバーターの構成
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983444"
---
# <a name="value-conversions"></a><span data-ttu-id="25b5b-103">値変換</span><span class="sxs-lookup"><span data-stu-id="25b5b-103">Value Conversions</span></span>

<span data-ttu-id="25b5b-104">値コンバーターを使用すると、データベースに対して読み取りまたは書き込みを行うときに、プロパティ値を変換できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="25b5b-105">この変換は、1つの値から同じ型の別の型 (文字列の暗号化など) にすることも、1つの型の値から別の型の値に変換することもできます (たとえば、列挙値をデータベース内の文字列に変換するなど)。</span><span class="sxs-lookup"><span data-stu-id="25b5b-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

> [!TIP]
> <span data-ttu-id="25b5b-106">このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)することで実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-106">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="overview"></a><span data-ttu-id="25b5b-107">概要</span><span class="sxs-lookup"><span data-stu-id="25b5b-107">Overview</span></span>

<span data-ttu-id="25b5b-108">値コンバーターは、との観点で指定され `ModelClrType` てい `ProviderClrType` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="25b5b-109">モデルの種類は、エンティティ型のプロパティの .NET 型です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="25b5b-110">プロバイダーの種類は、データベースプロバイダーによって認識される .NET 型です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="25b5b-111">たとえば、列挙型を文字列としてデータベースに保存する場合、モデルの種類は列挙型の型であり、プロバイダーの型は `String` です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="25b5b-112">これらの2つの型は同じにすることができます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-112">These two types can be the same.</span></span>

<span data-ttu-id="25b5b-113">変換は、からへの変換 `Func` `ModelClrType` `ProviderClrType` と、からへの2つの式ツリーを使用して定義され `ProviderClrType` `ModelClrType` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="25b5b-114">式ツリーは、効率的な変換のためにデータベースアクセスデリゲートにコンパイルできるように使用されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-114">Expression trees are used so that they can be compiled into the database access delegate for efficient conversions.</span></span> <span data-ttu-id="25b5b-115">式ツリーには、複雑な変換のための変換メソッドの単純な呼び出しを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-115">The expression tree may contain a simple call to a conversion method for complex conversions.</span></span>

> [!NOTE]
> <span data-ttu-id="25b5b-116">値の変換用に構成されたプロパティは、も指定する必要があり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-116">A property that has been configured for value conversion may also need to specify a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="25b5b-117">詳細については、次の例と [値の比較](xref:core/modeling/value-comparers) 子に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-117">See the examples below, and the [Value Comparers](xref:core/modeling/value-comparers) documentation for more information.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="25b5b-118">値コンバーターの構成</span><span class="sxs-lookup"><span data-stu-id="25b5b-118">Configuring a value converter</span></span>

<span data-ttu-id="25b5b-119">値の変換はで構成され <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-119">Value conversions are configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="25b5b-120">たとえば、次のように定義された列挙型とエンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-120">For example, consider an enum and entity type defined as:</span></span>

<!--
        public class Rider
        {
            public int Id { get; set; }
            public EquineBeast Mount { get; set; }
        }

        public enum EquineBeast
        {
            Donkey,
            Mule,
            Horse,
            Unicorn
        }
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

<span data-ttu-id="25b5b-121">では、 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 列挙値を "Donkey"、"Mule" などの文字列としてデータベースに格納するようにを構成できます。また、からに変換する1つの関数を指定するだけで、 `ModelClrType` 逆の `ProviderClrType` 変換については、次のような変換を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-121">Conversions can be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> to store the enum values as strings such as "Donkey", "Mule", etc. in the database; you simply need to provide one function which converts from the `ModelClrType` to the `ProviderClrType`, and another for the opposite conversion:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> <span data-ttu-id="25b5b-122">値は、 `null` 値コンバーターに渡されません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-122">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="25b5b-123">データベース列の null は、常にエンティティインスタンス内の null になります。また、その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-123">A null in a database column is always a null in the entity instance, and vice-versa.</span></span> <span data-ttu-id="25b5b-124">これにより、変換の実装が容易になり、null 許容のプロパティと null 非許容のプロパティ間で共有できるようになります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-124">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span> <span data-ttu-id="25b5b-125">詳細については、 [GitHub の問題 #13850](https://github.com/dotnet/efcore/issues/13850) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-125">See [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) for more information.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="25b5b-126">定義済みの変換</span><span class="sxs-lookup"><span data-stu-id="25b5b-126">Pre-defined conversions</span></span>

<span data-ttu-id="25b5b-127">EF Core には、変換関数を手動で記述する必要がないように定義済みの変換が多数含まれています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-127">EF Core contains many pre-defined conversions that avoid the need to write conversion functions manually.</span></span> <span data-ttu-id="25b5b-128">代わりに、EF Core は、モデルのプロパティの型および要求されたデータベースプロバイダーの種類に基づいて、使用する変換を選択します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-128">Instead, EF Core will pick the conversion to use based on the property type in the model and the requested database provider type.</span></span>

<span data-ttu-id="25b5b-129">たとえば、文字列の列挙型への変換は上の例として使用されますが、実際には、プロバイダーの種類が `string` のジェネリック型を使用するように構成されている場合に、EF Core によって自動的に実行され <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-129">For example, enum to string conversions are used as an example above, but EF Core will actually do this automatically when the provider type is configured as `string` using the generic type of <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

<span data-ttu-id="25b5b-130">データベース列の型を明示的に指定することで、同じことを実現できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-130">The same thing can be achieved by explicitly specifying the database column type.</span></span> <span data-ttu-id="25b5b-131">たとえば、エンティティ型が次のように定義されているとします。</span><span class="sxs-lookup"><span data-stu-id="25b5b-131">For example, if the entity type is defined like so:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="25b5b-132">データの注釈</span><span class="sxs-lookup"><span data-stu-id="25b5b-132">Data Annotations</span></span>](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[<span data-ttu-id="25b5b-133">Fluent API</span><span class="sxs-lookup"><span data-stu-id="25b5b-133">Fluent API</span></span>](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

<span data-ttu-id="25b5b-134">その後、列挙値は、で追加の構成を行わなくても、データベースに文字列として保存され <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-134">Then the enum values will be saved as strings in the database without any further configuration in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="25b5b-135">ValueConverter クラス</span><span class="sxs-lookup"><span data-stu-id="25b5b-135">The ValueConverter class</span></span>

<span data-ttu-id="25b5b-136"><xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>上に示すようにを呼び出すと、インスタンスが作成され <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 、プロパティに設定されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-136">Calling <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> as shown above will create a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance and set it on the property.</span></span> <span data-ttu-id="25b5b-137">代わりに、を `ValueConverter` 明示的に作成できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-137">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="25b5b-138">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-138">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

<span data-ttu-id="25b5b-139">これは、複数のプロパティで同じ変換を使用する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-139">This can be useful when multiple properties use the same conversion.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="25b5b-140">組み込みのコンバーター</span><span class="sxs-lookup"><span data-stu-id="25b5b-140">Built-in converters</span></span>

<span data-ttu-id="25b5b-141">前述のように、EF Core には、名前空間で定義されている一連の定義済みクラスが付属して <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> います。</span><span class="sxs-lookup"><span data-stu-id="25b5b-141">As mentioned above, EF Core ships with a set of pre-defined <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classes, found in the <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> namespace.</span></span> <span data-ttu-id="25b5b-142">多くの場合、EF は、前述のように、モデルのプロパティの型とデータベースで要求された型に基づいて、適切な組み込みのコンバーターを選択します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-142">In many cases EF will choose the appropriate built-in converter based on the type of the property in the model and the type requested in the database, as shown above for enums.</span></span> <span data-ttu-id="25b5b-143">たとえば、プロパティでを使用すると、 `.HasConversion<int>()` `bool` EF Core によってブール値が0と1の数値に変換されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-143">For example, using `.HasConversion<int>()` on a `bool` property will cause EF Core to convert bool values to numerical zero and one values:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

<span data-ttu-id="25b5b-144">これは、組み込みのインスタンスを作成し、それを明示的に設定することと同じです <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> 。</span><span class="sxs-lookup"><span data-stu-id="25b5b-144">This is functionally the same as creating an instance of the built-in <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> and setting it explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

<span data-ttu-id="25b5b-145">次の表は、一般的に使用される定義済みのモデル/プロパティ型からデータベースプロバイダーの型への変換をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="25b5b-145">The following table summarizes commonly-used pre-defined conversions from model/property types to database provider types.</span></span> <span data-ttu-id="25b5b-146">テーブルでは、、、、、、、、、、、、 `any_numeric_type` またはのいずれか `int` を意味 `short` `long` `byte` `uint` `ushort` `ulong` `sbyte` `char` `decimal` `float` `double` します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-146">In the table `any_numeric_type` means one of `int`, `short`, `long`, `byte`, `uint`, `ushort`, `ulong`, `sbyte`, `char`, `decimal`, `float`, or `double`.</span></span>

| <span data-ttu-id="25b5b-147">モデル/プロパティの型</span><span class="sxs-lookup"><span data-stu-id="25b5b-147">Model/property type</span></span> | <span data-ttu-id="25b5b-148">プロバイダー/データベースの種類</span><span class="sxs-lookup"><span data-stu-id="25b5b-148">Provider/database type</span></span> | <span data-ttu-id="25b5b-149">変換</span><span class="sxs-lookup"><span data-stu-id="25b5b-149">Conversion</span></span>                                                | <span data-ttu-id="25b5b-150">使用法</span><span class="sxs-lookup"><span data-stu-id="25b5b-150">Usage</span></span>
|:--------------------|------------------------|-----------------------------------------------------------|------
| <span data-ttu-id="25b5b-151">[bool]</span><span class="sxs-lookup"><span data-stu-id="25b5b-151">bool</span></span>                | <span data-ttu-id="25b5b-152">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-152">any_numeric_type</span></span>       | <span data-ttu-id="25b5b-153">False/true から0/1</span><span class="sxs-lookup"><span data-stu-id="25b5b-153">False/true to 0/1</span></span>                                         | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="25b5b-154">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-154">any_numeric_type</span></span>       | <span data-ttu-id="25b5b-155">任意の2つの数値に対する False/true</span><span class="sxs-lookup"><span data-stu-id="25b5b-155">False/true to any two numbers</span></span>                             | <span data-ttu-id="25b5b-156"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> を使用します</span><span class="sxs-lookup"><span data-stu-id="25b5b-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span></span>
|                     | <span data-ttu-id="25b5b-157">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-157">string</span></span>                 | <span data-ttu-id="25b5b-158">False/true から "Y"/"N" へ</span><span class="sxs-lookup"><span data-stu-id="25b5b-158">False/true to "Y"/"N"</span></span>                                     | `.HasConversion<string>()`
|                     | <span data-ttu-id="25b5b-159">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-159">string</span></span>                 | <span data-ttu-id="25b5b-160">任意の2つの文字列に対する False/true</span><span class="sxs-lookup"><span data-stu-id="25b5b-160">False/true to any two strings</span></span>                             | <span data-ttu-id="25b5b-161"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> を使用します</span><span class="sxs-lookup"><span data-stu-id="25b5b-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span></span>
| <span data-ttu-id="25b5b-162">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-162">any_numeric_type</span></span>    | <span data-ttu-id="25b5b-163">[bool]</span><span class="sxs-lookup"><span data-stu-id="25b5b-163">bool</span></span>                   | <span data-ttu-id="25b5b-164">0/1 から false/true へ</span><span class="sxs-lookup"><span data-stu-id="25b5b-164">0/1 to false/true</span></span>                                         | `.HasConversion<bool>()`
|                     | <span data-ttu-id="25b5b-165">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-165">any_numeric_type</span></span>       | <span data-ttu-id="25b5b-166">単純なキャスト</span><span class="sxs-lookup"><span data-stu-id="25b5b-166">Simple cast</span></span>                                               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="25b5b-167">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-167">string</span></span>                 | <span data-ttu-id="25b5b-168">数値を文字列として指定します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-168">The number as a string</span></span>                                    | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-169">列挙型</span><span class="sxs-lookup"><span data-stu-id="25b5b-169">Enum</span></span>                | <span data-ttu-id="25b5b-170">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-170">any_numeric_type</span></span>       | <span data-ttu-id="25b5b-171">列挙型の数値</span><span class="sxs-lookup"><span data-stu-id="25b5b-171">The numeric value of the enum</span></span>                             | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="25b5b-172">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-172">string</span></span>                 | <span data-ttu-id="25b5b-173">列挙値の文字列表現。</span><span class="sxs-lookup"><span data-stu-id="25b5b-173">The string representation of the enum value</span></span>               | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-174">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-174">string</span></span>              | <span data-ttu-id="25b5b-175">[bool]</span><span class="sxs-lookup"><span data-stu-id="25b5b-175">bool</span></span>                   | <span data-ttu-id="25b5b-176">文字列をブール値として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-176">Parses the string as a bool</span></span>                               | `.HasConversion<bool>()`
|                     | <span data-ttu-id="25b5b-177">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="25b5b-177">any_numeric_type</span></span>       | <span data-ttu-id="25b5b-178">文字列を指定された数値型として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-178">Parses the string as the given numeric type</span></span>               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="25b5b-179">char</span><span class="sxs-lookup"><span data-stu-id="25b5b-179">char</span></span>                   | <span data-ttu-id="25b5b-180">文字列の最初の文字</span><span class="sxs-lookup"><span data-stu-id="25b5b-180">The first character of the string</span></span>                         | `.HasConversion<char>()`
|                     | <span data-ttu-id="25b5b-181">DateTime</span><span class="sxs-lookup"><span data-stu-id="25b5b-181">DateTime</span></span>               | <span data-ttu-id="25b5b-182">文字列を DateTime として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-182">Parses the string as a DateTime</span></span>                           | `.HasConversion<DateTime>()`
|                     | <span data-ttu-id="25b5b-183">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="25b5b-183">DateTimeOffset</span></span>         | <span data-ttu-id="25b5b-184">文字列を DateTimeOffset として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-184">Parses the string as a DateTimeOffset</span></span>                     | `.HasConversion<DateTimeOffset>()`
|                     | <span data-ttu-id="25b5b-185">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="25b5b-185">TimeSpan</span></span>               | <span data-ttu-id="25b5b-186">文字列を TimeSpan として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-186">Parses the string as a TimeSpan</span></span>                           | `.HasConversion<TimeSpan>()`
|                     | <span data-ttu-id="25b5b-187">Guid</span><span class="sxs-lookup"><span data-stu-id="25b5b-187">Guid</span></span>                   | <span data-ttu-id="25b5b-188">文字列を Guid として解析します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-188">Parses the string as a Guid</span></span>                               | `.HasConversion<Guid>()`
|                     | <span data-ttu-id="25b5b-189">byte[]</span><span class="sxs-lookup"><span data-stu-id="25b5b-189">byte[]</span></span>                 | <span data-ttu-id="25b5b-190">文字列を UTF8 バイトとして指定します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-190">The string as UTF8 bytes</span></span>                                  | `.HasConversion<byte[]>()`
| <span data-ttu-id="25b5b-191">char</span><span class="sxs-lookup"><span data-stu-id="25b5b-191">char</span></span>                | <span data-ttu-id="25b5b-192">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-192">string</span></span>                 | <span data-ttu-id="25b5b-193">1つの文字列</span><span class="sxs-lookup"><span data-stu-id="25b5b-193">A single character string</span></span>                                 | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-194">DateTime</span><span class="sxs-lookup"><span data-stu-id="25b5b-194">DateTime</span></span>            | <span data-ttu-id="25b5b-195">long</span><span class="sxs-lookup"><span data-stu-id="25b5b-195">long</span></span>                   | <span data-ttu-id="25b5b-196">日付/時刻を保持する、DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="25b5b-196">Encoded date/time preserving DateTime.Kind</span></span>                | `.HasConversion<long>()`
|                     | <span data-ttu-id="25b5b-197">long</span><span class="sxs-lookup"><span data-stu-id="25b5b-197">long</span></span>                   | <span data-ttu-id="25b5b-198">クロック</span><span class="sxs-lookup"><span data-stu-id="25b5b-198">Ticks</span></span>                                                     | <span data-ttu-id="25b5b-199"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> を使用します</span><span class="sxs-lookup"><span data-stu-id="25b5b-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span></span>
|                     | <span data-ttu-id="25b5b-200">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-200">string</span></span>                 | <span data-ttu-id="25b5b-201">インバリアントカルチャの日付/時刻文字列</span><span class="sxs-lookup"><span data-stu-id="25b5b-201">Invariant culture date/time string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-202">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="25b5b-202">DateTimeOffset</span></span>      | <span data-ttu-id="25b5b-203">long</span><span class="sxs-lookup"><span data-stu-id="25b5b-203">long</span></span>                   | <span data-ttu-id="25b5b-204">オフセット付きのエンコードされた日付/時刻</span><span class="sxs-lookup"><span data-stu-id="25b5b-204">Encoded date/time with offset</span></span>                             | `.HasConversion<long>()`
|                     | <span data-ttu-id="25b5b-205">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-205">string</span></span>                 | <span data-ttu-id="25b5b-206">オフセットを含むインバリアントカルチャの日付/時刻文字列</span><span class="sxs-lookup"><span data-stu-id="25b5b-206">Invariant culture date/time string with offset</span></span>            | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-207">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="25b5b-207">TimeSpan</span></span>            | <span data-ttu-id="25b5b-208">long</span><span class="sxs-lookup"><span data-stu-id="25b5b-208">long</span></span>                   | <span data-ttu-id="25b5b-209">クロック</span><span class="sxs-lookup"><span data-stu-id="25b5b-209">Ticks</span></span>                                                     | `.HasConversion<long>()`
|                     | <span data-ttu-id="25b5b-210">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-210">string</span></span>                 | <span data-ttu-id="25b5b-211">インバリアントカルチャの期間文字列</span><span class="sxs-lookup"><span data-stu-id="25b5b-211">Invariant culture time span string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-212">Uri</span><span class="sxs-lookup"><span data-stu-id="25b5b-212">Uri</span></span>                 | <span data-ttu-id="25b5b-213">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-213">string</span></span>                 | <span data-ttu-id="25b5b-214">URI を文字列として指定します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-214">The URI as a string</span></span>                                       | `.HasConversion<string>()`
| <span data-ttu-id="25b5b-215">PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="25b5b-215">PhysicalAddress</span></span>     | <span data-ttu-id="25b5b-216">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-216">string</span></span>                 | <span data-ttu-id="25b5b-217">アドレスが文字列として指定されています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-217">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="25b5b-218">byte[]</span><span class="sxs-lookup"><span data-stu-id="25b5b-218">byte[]</span></span>                 | <span data-ttu-id="25b5b-219">ビッグエンディアンネットワークの順序のバイト数</span><span class="sxs-lookup"><span data-stu-id="25b5b-219">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="25b5b-220">IPAddress</span><span class="sxs-lookup"><span data-stu-id="25b5b-220">IPAddress</span></span>           | <span data-ttu-id="25b5b-221">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-221">string</span></span>                 | <span data-ttu-id="25b5b-222">アドレスが文字列として指定されています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-222">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="25b5b-223">byte[]</span><span class="sxs-lookup"><span data-stu-id="25b5b-223">byte[]</span></span>                 | <span data-ttu-id="25b5b-224">ビッグエンディアンネットワークの順序のバイト数</span><span class="sxs-lookup"><span data-stu-id="25b5b-224">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="25b5b-225">Guid</span><span class="sxs-lookup"><span data-stu-id="25b5b-225">Guid</span></span>                | <span data-ttu-id="25b5b-226">string</span><span class="sxs-lookup"><span data-stu-id="25b5b-226">string</span></span>                 | <span data-ttu-id="25b5b-227">' Dddddddd-dddd-dddd-dddd-dddddddddddd ' 形式の GUID</span><span class="sxs-lookup"><span data-stu-id="25b5b-227">The GUID in 'dddddddd-dddd-dddd-dddd-dddddddddddd' format</span></span> | `.HasConversion<string>()`
|                     | <span data-ttu-id="25b5b-228">byte[]</span><span class="sxs-lookup"><span data-stu-id="25b5b-228">byte[]</span></span>                 | <span data-ttu-id="25b5b-229">.NET バイナリシリアル化の順序のバイト数</span><span class="sxs-lookup"><span data-stu-id="25b5b-229">Bytes in .NET binary serialization order</span></span>                  | `.HasConversion<byte[]>()`

<span data-ttu-id="25b5b-230">これらの変換では、値の形式が変換に適していると想定しています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-230">Note that these conversions assume that the format of the value is appropriate for the conversion.</span></span> <span data-ttu-id="25b5b-231">たとえば、文字列値を数値として解析できない場合、文字列を数値に変換しても失敗します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-231">For example, converting strings to numbers will fail if the string values cannot be parsed as numbers.</span></span>

<span data-ttu-id="25b5b-232">組み込みのコンバーターの完全な一覧は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="25b5b-232">The full list of built-in converters is:</span></span>

* <span data-ttu-id="25b5b-233">ブール型プロパティの変換:</span><span class="sxs-lookup"><span data-stu-id="25b5b-233">Converting bool properties:</span></span>
  * <span data-ttu-id="25b5b-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool to string ("Y"、"N" など)</span><span class="sxs-lookup"><span data-stu-id="25b5b-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> - Bool to strings such as "Y" and "N"</span></span>
  * <span data-ttu-id="25b5b-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool から任意の2つの値へ</span><span class="sxs-lookup"><span data-stu-id="25b5b-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> - Bool to any two values</span></span>
  * <span data-ttu-id="25b5b-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool から0および1</span><span class="sxs-lookup"><span data-stu-id="25b5b-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> - Bool to zero and one</span></span>
* <span data-ttu-id="25b5b-237">バイト配列プロパティの変換:</span><span class="sxs-lookup"><span data-stu-id="25b5b-237">Converting byte array properties:</span></span>
  * <span data-ttu-id="25b5b-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Base64 でエンコードされた文字列へのバイト配列</span><span class="sxs-lookup"><span data-stu-id="25b5b-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="25b5b-239">型キャストのみを必要とする変換</span><span class="sxs-lookup"><span data-stu-id="25b5b-239">Any conversion that requires only a type-cast</span></span>
  * <span data-ttu-id="25b5b-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -型キャストのみを必要とする変換</span><span class="sxs-lookup"><span data-stu-id="25b5b-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> - Conversions that require only a type cast</span></span>
* <span data-ttu-id="25b5b-241">Char プロパティの変換:</span><span class="sxs-lookup"><span data-stu-id="25b5b-241">Converting char properties:</span></span>
  * <span data-ttu-id="25b5b-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char から単一の文字列へ</span><span class="sxs-lookup"><span data-stu-id="25b5b-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> - Char to single character string</span></span>
* <span data-ttu-id="25b5b-243">プロパティの変換 <xref:System.DateTimeOffset> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-243">Converting <xref:System.DateTimeOffset> properties:</span></span>
  * <span data-ttu-id="25b5b-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> バイナリエンコードされた64ビット値に</span><span class="sxs-lookup"><span data-stu-id="25b5b-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> to binary-encoded 64-bit value</span></span>
  * <span data-ttu-id="25b5b-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> バイト配列への</span><span class="sxs-lookup"><span data-stu-id="25b5b-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> to byte array</span></span>
  * <span data-ttu-id="25b5b-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> to string</span></span>
* <span data-ttu-id="25b5b-247">プロパティの変換 <xref:System.DateTime> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-247">Converting <xref:System.DateTime> properties:</span></span>
  * <span data-ttu-id="25b5b-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> Datetimekind.utc を含む64ビット値に</span><span class="sxs-lookup"><span data-stu-id="25b5b-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> to 64-bit value including DateTimeKind</span></span>
  * <span data-ttu-id="25b5b-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> to string</span></span>
  * <span data-ttu-id="25b5b-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> ティックに</span><span class="sxs-lookup"><span data-stu-id="25b5b-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> to ticks</span></span>
* <span data-ttu-id="25b5b-251">列挙型プロパティの変換:</span><span class="sxs-lookup"><span data-stu-id="25b5b-251">Converting enum properties:</span></span>
  * <span data-ttu-id="25b5b-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -基になる数値の列挙型</span><span class="sxs-lookup"><span data-stu-id="25b5b-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> - Enum to underlying number</span></span>
  * <span data-ttu-id="25b5b-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -文字列への列挙</span><span class="sxs-lookup"><span data-stu-id="25b5b-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> - Enum to string</span></span>
* <span data-ttu-id="25b5b-254">プロパティの変換 <xref:System.Guid> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-254">Converting <xref:System.Guid> properties:</span></span>
  * <span data-ttu-id="25b5b-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> バイト配列への</span><span class="sxs-lookup"><span data-stu-id="25b5b-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> to byte array</span></span>
  * <span data-ttu-id="25b5b-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> to string</span></span>
* <span data-ttu-id="25b5b-257">プロパティの変換 <xref:System.Net.IPAddress> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-257">Converting <xref:System.Net.IPAddress> properties:</span></span>
  * <span data-ttu-id="25b5b-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> バイト配列への</span><span class="sxs-lookup"><span data-stu-id="25b5b-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> to byte array</span></span>
  * <span data-ttu-id="25b5b-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> to string</span></span>
* <span data-ttu-id="25b5b-260">数値 (int、double、decimal など) のプロパティを変換します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-260">Converting numeric (int, double, decimal, etc.) properties:</span></span>
  * <span data-ttu-id="25b5b-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -バイト配列に対する任意の数値</span><span class="sxs-lookup"><span data-stu-id="25b5b-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> - Any numerical value to byte array</span></span>
  * <span data-ttu-id="25b5b-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -文字列に対する任意の数値</span><span class="sxs-lookup"><span data-stu-id="25b5b-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> - Any numerical value to string</span></span>
* <span data-ttu-id="25b5b-263">プロパティの変換 <xref:System.Net.NetworkInformation.PhysicalAddress> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-263">Converting <xref:System.Net.NetworkInformation.PhysicalAddress> properties:</span></span>
  * <span data-ttu-id="25b5b-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> バイト配列への</span><span class="sxs-lookup"><span data-stu-id="25b5b-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to byte array</span></span>
  * <span data-ttu-id="25b5b-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to string</span></span>
* <span data-ttu-id="25b5b-266">文字列プロパティの変換:</span><span class="sxs-lookup"><span data-stu-id="25b5b-266">Converting string properties:</span></span>
  * <span data-ttu-id="25b5b-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -"Y" や "N" などの文字列を bool に</span><span class="sxs-lookup"><span data-stu-id="25b5b-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> - Strings such as "Y" and "N" to bool</span></span>
  * <span data-ttu-id="25b5b-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -文字列から UTF8 バイトへ</span><span class="sxs-lookup"><span data-stu-id="25b5b-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> - String to UTF8 bytes</span></span>
  * <span data-ttu-id="25b5b-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -文字列から文字へ</span><span class="sxs-lookup"><span data-stu-id="25b5b-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> - String to character</span></span>
  * <span data-ttu-id="25b5b-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -文字列から <xref:System.DateTime></span><span class="sxs-lookup"><span data-stu-id="25b5b-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> - String to <xref:System.DateTime></span></span>
  * <span data-ttu-id="25b5b-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -文字列から <xref:System.DateTimeOffset></span><span class="sxs-lookup"><span data-stu-id="25b5b-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> - String to <xref:System.DateTimeOffset></span></span>
  * <span data-ttu-id="25b5b-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -列挙する文字列</span><span class="sxs-lookup"><span data-stu-id="25b5b-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> - String to enum</span></span>
  * <span data-ttu-id="25b5b-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -文字列から <xref:System.Guid></span><span class="sxs-lookup"><span data-stu-id="25b5b-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> - String to <xref:System.Guid></span></span>
  * <span data-ttu-id="25b5b-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -文字列から数値型</span><span class="sxs-lookup"><span data-stu-id="25b5b-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> - String to numeric type</span></span>
  * <span data-ttu-id="25b5b-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -文字列から <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="25b5b-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> - String to <xref:System.TimeSpan></span></span>
  * <span data-ttu-id="25b5b-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -文字列から <xref:System.Uri></span><span class="sxs-lookup"><span data-stu-id="25b5b-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> - String to <xref:System.Uri></span></span>
* <span data-ttu-id="25b5b-277">プロパティの変換 <xref:System.TimeSpan> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-277">Converting <xref:System.TimeSpan> properties:</span></span>
  * <span data-ttu-id="25b5b-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> to string</span></span>
  * <span data-ttu-id="25b5b-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> ティックに</span><span class="sxs-lookup"><span data-stu-id="25b5b-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> to ticks</span></span>
* <span data-ttu-id="25b5b-280">プロパティの変換 <xref:System.Uri> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-280">Converting <xref:System.Uri> properties:</span></span>
  * <span data-ttu-id="25b5b-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> 文字列に</span><span class="sxs-lookup"><span data-stu-id="25b5b-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> to string</span></span>

<span data-ttu-id="25b5b-282">組み込みのコンバーターはすべてステートレスであるため、1つのインスタンスを複数のプロパティで安全に共有できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-282">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="column-facets-and-mapping-hints"></a><span data-ttu-id="25b5b-283">列のファセットとマッピングヒント</span><span class="sxs-lookup"><span data-stu-id="25b5b-283">Column facets and mapping hints</span></span>

<span data-ttu-id="25b5b-284">一部のデータベースの種類には、データの格納方法を変更するファセットがあります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-284">Some database types have facets that modify how the data is stored.</span></span> <span data-ttu-id="25b5b-285">次の設定があります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-285">These include:</span></span>

* <span data-ttu-id="25b5b-286">Decimal および date/time 列の有効桁数と小数点以下桁数</span><span class="sxs-lookup"><span data-stu-id="25b5b-286">Precision and scale for decimals and date/time columns</span></span>
* <span data-ttu-id="25b5b-287">バイナリおよび文字列型の列のサイズ/長さ</span><span class="sxs-lookup"><span data-stu-id="25b5b-287">Size/length for binary and string columns</span></span>
* <span data-ttu-id="25b5b-288">文字列型の列の場合は Unicode</span><span class="sxs-lookup"><span data-stu-id="25b5b-288">Unicode for string columns</span></span>

<span data-ttu-id="25b5b-289">これらのファセットは、値コンバーターを使用するプロパティの通常の方法で構成でき、変換後のデータベース型に適用されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-289">These facets can be configured in the normal way for a property that uses a value converter, and will apply to the converted database type.</span></span> <span data-ttu-id="25b5b-290">たとえば、列挙型から文字列への変換では、データベース列が非 Unicode で、最大20文字を格納するように指定できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-290">For example, when converting from an enum to strings, we can specify that the database column should be non-Unicode and store up to 20 characters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

<span data-ttu-id="25b5b-291">または、コンバーターを明示的に作成する場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="25b5b-291">Or, when creating the converter explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

<span data-ttu-id="25b5b-292">この結果、 `varchar(20)` SQL Server に対して EF Core 移行を使用すると、列が表示されるようになります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-292">This results in a `varchar(20)` column when using EF Core migrations against SQL Server:</span></span>

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

<span data-ttu-id="25b5b-293">ただし、既定ですべての列がである場合は、 `EquineBeast` `varchar(20)` この情報をとして値コンバーターに与えることができ <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-293">However, if by default all `EquineBeast` columns should be `varchar(20)`, then this information can be given to the value converter as a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints>.</span></span> <span data-ttu-id="25b5b-294">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-294">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

<span data-ttu-id="25b5b-295">このコンバーターが使用されると、データベース列は unicode 以外の値になり、最大長は20になります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-295">Now any time this converter is used, the database column will be non-unicode with a max length of 20.</span></span> <span data-ttu-id="25b5b-296">ただし、これらは、マップされたプロパティに明示的に設定されているファセットによってオーバーライドされるため、ヒントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-296">However, these are only hints since they are be overridden by any facets explicitly set on the mapped property.</span></span>

## <a name="examples"></a><span data-ttu-id="25b5b-297">例</span><span class="sxs-lookup"><span data-stu-id="25b5b-297">Examples</span></span>

### <a name="simple-value-objects"></a><span data-ttu-id="25b5b-298">単純値オブジェクト</span><span class="sxs-lookup"><span data-stu-id="25b5b-298">Simple value objects</span></span>

<span data-ttu-id="25b5b-299">この例では、単純型を使用してプリミティブ型をラップします。</span><span class="sxs-lookup"><span data-stu-id="25b5b-299">This example uses a simple type to wrap a primitive type.</span></span> <span data-ttu-id="25b5b-300">これは、モデル内の型をプリミティブ型よりも具体的な (つまり、よりタイプセーフな) 必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-300">This can be useful when you want the type in your model to be more specific (and hence more type-safe) than a primitive type.</span></span> <span data-ttu-id="25b5b-301">この例では、この型は `Dollars` で、10進プリミティブをラップします。</span><span class="sxs-lookup"><span data-stu-id="25b5b-301">In this example, that type is `Dollars`, which wraps the decimal primitive:</span></span>

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

<span data-ttu-id="25b5b-302">これは、エンティティ型で使用できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-302">This can be used in an entity type:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

<span data-ttu-id="25b5b-303">データベースに格納されている場合は、基になるに変換 `decimal` されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-303">And converted to the underlying `decimal` when stored in the database:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> <span data-ttu-id="25b5b-304">この値オブジェクトは、 [readonly 構造体](/dotnet/csharp/language-reference/builtin-types/struct)として実装されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-304">This value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="25b5b-305">これは、EF Core が問題なくスナップショットして値を比較できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-305">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="25b5b-306">詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-306">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="composite-value-objects"></a><span data-ttu-id="25b5b-307">複合値オブジェクト</span><span class="sxs-lookup"><span data-stu-id="25b5b-307">Composite value objects</span></span>

<span data-ttu-id="25b5b-308">前の例では、値オブジェクトの型には1つのプロパティのみが含まれていました。</span><span class="sxs-lookup"><span data-stu-id="25b5b-308">In the previous example, the value object type contained only a single property.</span></span> <span data-ttu-id="25b5b-309">値オブジェクト型では、ドメインの概念を形成する複数のプロパティを構成するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="25b5b-309">It is more common for a value object type to compose multiple properties that together form a domain concept.</span></span> <span data-ttu-id="25b5b-310">たとえば、金額と通貨の両方を含む一般的な型は次のように `Money` なります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-310">For example, a general `Money` type that contains both the amount and the currency:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

<span data-ttu-id="25b5b-311">この値オブジェクトは、次のようにエンティティ型で使用できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-311">This value object can be used in an entity type as before:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

<span data-ttu-id="25b5b-312">現在、値コンバーターでは、単一のデータベース列との間でのみ値を変換できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-312">Value converters can currently only convert values to and from a single database column.</span></span> <span data-ttu-id="25b5b-313">この制限は、オブジェクトのすべてのプロパティ値を1つの列値にエンコードする必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-313">This limitation means that all property values from the object must be encoded into a single column value.</span></span> <span data-ttu-id="25b5b-314">通常、この処理は、データベースへの移動時にオブジェクトをシリアル化し、その後で逆シリアル化することによって処理されます。たとえば、次のように使用し <xref:System.Text.Json> ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-314">This is typically handled by serializing the object as it goes into the database, and then deserializing it again on the way out. For example, using <xref:System.Text.Json>:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> <span data-ttu-id="25b5b-315">EF Core 6.0 では、オブジェクトを複数の列にマップできるようにすることを計画しています。これにより、シリアル化を使用する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-315">We plan to allow mapping an object to multiple columns in EF Core 6.0, removing the need to use serialization here.</span></span> <span data-ttu-id="25b5b-316">これは、 [GitHub の問題 #13947](https://github.com/dotnet/efcore/issues/13947)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-316">This is tracked by [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947).</span></span>

> [!NOTE]
> <span data-ttu-id="25b5b-317">前の例と同様に、この値オブジェクトは [readonly 構造体](/dotnet/csharp/language-reference/builtin-types/struct)として実装されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-317">As with the previous example, this value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="25b5b-318">これは、EF Core が問題なくスナップショットして値を比較できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-318">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="25b5b-319">詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-319">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-primitives"></a><span data-ttu-id="25b5b-320">プリミティブのコレクション</span><span class="sxs-lookup"><span data-stu-id="25b5b-320">Collections of primitives</span></span>

<span data-ttu-id="25b5b-321">シリアル化を使用して、プリミティブ値のコレクションを格納することもできます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-321">Serialization can also be used to store a collection of primitive values.</span></span> <span data-ttu-id="25b5b-322">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-322">For example:</span></span>

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

<span data-ttu-id="25b5b-323">もう一度を使用する <xref:System.Text.Json> :</span><span class="sxs-lookup"><span data-stu-id="25b5b-323">Using <xref:System.Text.Json> again:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

<span data-ttu-id="25b5b-324">`ICollection<string>` 変更可能な参照型を表します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-324">`ICollection<string>` represents a mutable reference type.</span></span> <span data-ttu-id="25b5b-325">これは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> EF Core が変更を追跡して正しく検出できるように、が必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-325">This means that a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> is needed so that EF Core can track and detect changes correctly.</span></span> <span data-ttu-id="25b5b-326">詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-326">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-value-objects"></a><span data-ttu-id="25b5b-327">値オブジェクトのコレクション</span><span class="sxs-lookup"><span data-stu-id="25b5b-327">Collections of value objects</span></span>

<span data-ttu-id="25b5b-328">前の2つの例を組み合わせて、値オブジェクトのコレクションを作成できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-328">Combining the previous two examples together we can create a collection of value objects.</span></span> <span data-ttu-id="25b5b-329">たとえば、 `AnnualFinance` 1 年間、ブログの金融をモデル化する型について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="25b5b-329">For example, consider an `AnnualFinance` type that models blog finances for a single year:</span></span>

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

<span data-ttu-id="25b5b-330">この型は、前に作成したいくつかの型を結合し `Money` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-330">This type composes several of the `Money` types we created previously:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

<span data-ttu-id="25b5b-331">次に、のコレクションを `AnnualFinance` エンティティ型に追加できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-331">We can then add a collection of `AnnualFinance` to our entity type:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

<span data-ttu-id="25b5b-332">次に、シリアル化を使用して次のものを格納します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-332">And again use serialization to store this:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> <span data-ttu-id="25b5b-333">以前と同様に、この変換にはが必要です <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。</span><span class="sxs-lookup"><span data-stu-id="25b5b-333">As before, this conversion requires a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="25b5b-334">詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-334">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="value-objects-as-keys"></a><span data-ttu-id="25b5b-335">キーとしての値オブジェクト</span><span class="sxs-lookup"><span data-stu-id="25b5b-335">Value objects as keys</span></span>

<span data-ttu-id="25b5b-336">値を割り当てるときにタイプセーフレベルを追加するために、プリミティブキープロパティを値オブジェクトにラップすることがあります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-336">Sometimes primitive key properties may be wrapped in value objects to add an additional level of type-safety in assigning values.</span></span> <span data-ttu-id="25b5b-337">たとえば、ブログのキーの種類と、投稿用のキーの種類を実装できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-337">For example, we could implement a key type for blogs, and a key type for posts:</span></span>

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

<span data-ttu-id="25b5b-338">これらは、ドメインモデルで使用できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-338">These can then be used in the domain model:</span></span>

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

<span data-ttu-id="25b5b-339">は誤ってに割り当てられず、誤ってに割り当てられないことに注意 `Blog.Id` `PostKey` して `Post.Id` `BlogKey` ください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-339">Notice that `Blog.Id` cannot accidentally be assigned a `PostKey`, and `Post.Id` cannot accidentally be assigned a `BlogKey`.</span></span> <span data-ttu-id="25b5b-340">同様に、 `Post.BlogId` 外部キープロパティにはが割り当てられている必要があり `BlogKey` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-340">Similarly, the `Post.BlogId` foreign key property must be assigned a `BlogKey`.</span></span>

> [!NOTE]
> <span data-ttu-id="25b5b-341">このパターンを表示することは、お勧めしません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-341">Showing this pattern does not mean we recommend it.</span></span> <span data-ttu-id="25b5b-342">このレベルの抽象化が開発環境を支援または hampering しているかどうかを慎重に検討してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-342">Carefully consider whether this level of abstraction is helping or hampering your development experience.</span></span> <span data-ttu-id="25b5b-343">また、キー値を直接処理するのではなく、ナビゲーションと生成されたキーの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-343">Also, consider using navigations and generated keys instead of dealing with key values directly.</span></span>

<span data-ttu-id="25b5b-344">これらのキープロパティは、値コンバーターを使用してマップできます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-344">These key properties can then be mapped using value converters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> <span data-ttu-id="25b5b-345">現在、変換のキープロパティは、生成されたキー値を使用できません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-345">Currently key properties with conversions cannot use generated key values.</span></span> <span data-ttu-id="25b5b-346">この制限を解除するには、 [GitHub の問題](https://github.com/dotnet/efcore/issues/11597) に投票 #11597。</span><span class="sxs-lookup"><span data-stu-id="25b5b-346">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) to have this limitation removed.</span></span>

### <a name="use-ulong-for-timestamprowversion"></a><span data-ttu-id="25b5b-347">Timestamp/rowversion に ulong を使用する</span><span class="sxs-lookup"><span data-stu-id="25b5b-347">Use ulong for timestamp/rowversion</span></span>

<span data-ttu-id="25b5b-348">SQL Server は[、8バイトのバイナリ `rowversion` / `timestamp` 列](/sql/t-sql/data-types/rowversion-transact-sql)を使用した自動[オプティミスティック同時実行制御](xref:core/saving/concurrency)をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-348">SQL Server supports automatic [optimistic concurrency](xref:core/saving/concurrency) using [8-byte binary `rowversion`/`timestamp` columns](/sql/t-sql/data-types/rowversion-transact-sql).</span></span> <span data-ttu-id="25b5b-349">これらは常に、8バイトの配列を使用してデータベースに対して読み書きされます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-349">These are always read from and written to the database using an 8-byte array.</span></span> <span data-ttu-id="25b5b-350">ただし、バイト配列は変更可能な参照型であるため、処理が多少困難になります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-350">However, byte arrays are a mutable reference type, which makes them somewhat painful to deal with.</span></span> <span data-ttu-id="25b5b-351">値コンバーターを使用すると、 `rowversion` をプロパティに割り当てること `ulong` ができます。これは、バイト配列よりも適切で使いやすくなります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-351">Value converters allow the `rowversion` to instead be mapped to a `ulong` property, which is much more appropriate and easy to use than the byte array.</span></span> <span data-ttu-id="25b5b-352">たとえば、 `Blog` ulong 同時実行トークンを持つエンティティについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-352">For example, consider a `Blog` entity with a ulong concurrency token:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

<span data-ttu-id="25b5b-353">`rowversion`値コンバーターを使用して SQL server 列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-353">This can be mapped to a SQL server `rowversion` column using a value converter:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a><span data-ttu-id="25b5b-354">日付を読み取るときに DateTime を指定します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-354">Specify the DateTime.Kind when reading dates</span></span>

<span data-ttu-id="25b5b-355">SQL Server <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> は、を <xref:System.DateTime> またはとして格納するときに、フラグを破棄し [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-355">SQL Server discards the <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag when storing a <xref:System.DateTime> as a [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) or [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql).</span></span> <span data-ttu-id="25b5b-356">つまり、データベースから返される DateTime 値には、常に <xref:System.DateTimeKind> のが含ま `Unspecified` れます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-356">This means that DateTime values coming back from the database always have a <xref:System.DateTimeKind> of `Unspecified`.</span></span>

<span data-ttu-id="25b5b-357">値コンバーターは、次の2つの方法で使用できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-357">Value converters can be used in two ways to deal with this.</span></span> <span data-ttu-id="25b5b-358">まず、EF Core には、フラグを保持する8バイトの不透明な値を作成する値コンバーターがあり `Kind` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-358">First, EF Core has a value converter that creates an 8-byte opaque value which preserves the `Kind` flag.</span></span> <span data-ttu-id="25b5b-359">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-359">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

<span data-ttu-id="25b5b-360">これにより、異なるフラグを持つ DateTime 値 `Kind` をデータベースに混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-360">This allows DateTime values with different `Kind` flags to be mixed in the database.</span></span>

<span data-ttu-id="25b5b-361">この方法の問題は、データベースで認識できる `datetime` 列または列がなくなったことです `datetime2` 。</span><span class="sxs-lookup"><span data-stu-id="25b5b-361">The problem with this approach is that the database no longer has recognizable `datetime` or `datetime2` columns.</span></span> <span data-ttu-id="25b5b-362">そのため、通常は UTC 時刻 (または、通常はあまりローカル時刻) を格納し、次にフラグを無視するか、 `Kind` 値コンバーターを使用して適切な値に設定します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-362">So instead it is common to always store UTC time (or, less commonly, always local time) and then either ignore the `Kind` flag or set it to the appropriate value using a value converter.</span></span> <span data-ttu-id="25b5b-363">たとえば、次のコンバーターを使用すると、 `DateTime` データベースから読み取られた値が次のようになり <xref:System.DateTimeKind> `UTC` ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-363">For example, the converter below ensures that the `DateTime` value read from the database will have the <xref:System.DateTimeKind> `UTC`:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

<span data-ttu-id="25b5b-364">ローカルと UTC の両方の値がエンティティインスタンスで設定されている場合は、挿入前にコンバーターを使用して適切に変換できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-364">If a mix of local and UTC values are being set in entity instances, then the converter can be used to convert appropriately before inserting.</span></span> <span data-ttu-id="25b5b-365">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-365">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> <span data-ttu-id="25b5b-366">常に UTC 時間を使用するようにすべてのデータベースアクセスコードを統合することを検討してください。ユーザーにデータを表示するときは、現地時間だけを扱います。</span><span class="sxs-lookup"><span data-stu-id="25b5b-366">Carefully consider unifying all database access code to use UTC time all the time, only dealing with local time when presenting data to users.</span></span>

### <a name="use-case-insensitive-string-keys"></a><span data-ttu-id="25b5b-367">大文字と小文字を区別しない文字列キーを使用する</span><span class="sxs-lookup"><span data-stu-id="25b5b-367">Use case-insensitive string keys</span></span>

<span data-ttu-id="25b5b-368">SQL Server を含む一部のデータベースでは、既定で大文字と小文字を区別しない文字列比較が実行されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-368">Some databases, including SQL Server, perform case-insensitive string comparisons by default.</span></span> <span data-ttu-id="25b5b-369">一方、.NET では、既定で大文字と小文字を区別した文字列比較が実行されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-369">.NET, on the other hand, performs case-sensitive string comparisons by default.</span></span> <span data-ttu-id="25b5b-370">つまり、"DotNet" のような外部キー値は SQL Server の主キー値 "DotNet" と一致しますが、EF Core では一致しません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-370">This means that a foreign key value like "DotNet" will match the primary key value "dotnet" on SQL Server, but will not match it in EF Core.</span></span> <span data-ttu-id="25b5b-371">キーの値の比較子を使用すると、データベースのように大文字と小文字を区別せずに文字列を比較することができ EF Core ます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-371">A value comparer for keys can be used to force EF Core into case-insensitive string comparisons like in the database.</span></span> <span data-ttu-id="25b5b-372">たとえば、次のような文字列キーを持つブログ/投稿モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-372">For example, consider a blog/posts model with string keys:</span></span>

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

<span data-ttu-id="25b5b-373">一部の `Post.BlogId` 値の大文字と小文字が異なる場合、予期したとおりに動作しません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-373">This will not work as expected if some of the `Post.BlogId` values have different casing.</span></span> <span data-ttu-id="25b5b-374">これによって発生するエラーは、アプリケーションの動作によって異なりますが、通常は [固定](xref:core/change-tracking/relationship-changes) されていないオブジェクトのグラフや、FK 値が間違っているために失敗した更新などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-374">The errors caused by this will depend on what the application is doing, but typically involve graphs of objects that are not [fixed-up](xref:core/change-tracking/relationship-changes) correctly, and/or updates that fail because the FK value is wrong.</span></span> <span data-ttu-id="25b5b-375">値の比較子を使用して、この問題を修正できます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-375">A value comparer can be used to correct this:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> <span data-ttu-id="25b5b-376">.NET の文字列比較とデータベース文字列の比較は、大文字小文字の区別だけではありません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-376">.NET string comparisons and database string comparisons can differ in more than just case sensitivity.</span></span> <span data-ttu-id="25b5b-377">このパターンは、単純な ASCII キーに対して機能しますが、カルチャ固有の文字を含むキーに対しては失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-377">This pattern works for simple ASCII keys, but may fail for keys with any kind of culture-specific characters.</span></span> <span data-ttu-id="25b5b-378">詳細については [、「照合順序と大文字小文字の区別](xref:core/miscellaneous/collations-and-case-sensitivity) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-378">See [Collations and Case Sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity) for more information.</span></span>

### <a name="handle-fixed-length-database-strings"></a><span data-ttu-id="25b5b-379">固定長のデータベース文字列を処理する</span><span class="sxs-lookup"><span data-stu-id="25b5b-379">Handle fixed-length database strings</span></span>

<span data-ttu-id="25b5b-380">前の例では、値コンバーターは必要ありませんでした。</span><span class="sxs-lookup"><span data-stu-id="25b5b-380">The previous example did not need a value converter.</span></span> <span data-ttu-id="25b5b-381">ただし、コンバーターは、やなどの固定長のデータベース文字列型に便利 `char(20)` です `nchar(20)` 。</span><span class="sxs-lookup"><span data-stu-id="25b5b-381">However, a converter can be useful for fixed-length database string types like `char(20)` or `nchar(20)`.</span></span> <span data-ttu-id="25b5b-382">固定長の文字列は、データベースに値が挿入されるたびに、完全な長さに埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-382">Fixed-length strings are padded to their full length whenever a value is inserted into the database.</span></span> <span data-ttu-id="25b5b-383">つまり、キー値 "" は、"" `dotnet` としてデータベースから読み取られ `dotnet..............` ます。ここで、は `.` 空白文字を表します。</span><span class="sxs-lookup"><span data-stu-id="25b5b-383">This means that a key value of "`dotnet`" will be read back from the database as "`dotnet..............`", where `.` represents a space character.</span></span> <span data-ttu-id="25b5b-384">この場合、埋め込まれていないキー値と正しく比較されません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-384">This will then not compare correctly with key values that are not padded.</span></span>

<span data-ttu-id="25b5b-385">値コンバーターを使用して、キー値の読み取り時にパディングをトリミングできます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-385">A value converter can be used to trim the padding when reading key values.</span></span> <span data-ttu-id="25b5b-386">これを前の例の値比較子と組み合わせて、固定長の大文字と小文字を区別しない ASCII キーを正しく比較することができます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-386">This can be combined with the value comparer in the previous example to compare fixed length case-insensitive ASCII keys correctly.</span></span> <span data-ttu-id="25b5b-387">例:</span><span class="sxs-lookup"><span data-stu-id="25b5b-387">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a><span data-ttu-id="25b5b-388">プロパティ値の暗号化</span><span class="sxs-lookup"><span data-stu-id="25b5b-388">Encrypt property values</span></span>

<span data-ttu-id="25b5b-389">値コンバーターを使用して、データベースに送信する前にプロパティ値を暗号化し、その後で復号化することができます。たとえば、実際の暗号化アルゴリズムの代替として文字列の反転を使用する場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="25b5b-389">Value converters can be used to encrypt property values before sending them to the database, and then decrypt them on the way out. For example, using string reversal as a substitute for a real encryption algorithm:</span></span>

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> <span data-ttu-id="25b5b-390">現在の DbContext (またはその他のセッション状態) への参照を値コンバーター内から取得する方法は現在ありません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-390">There is currently no way to get a reference to the current DbContext, or other session state, from within a value converter.</span></span> <span data-ttu-id="25b5b-391">これにより、使用できる暗号化の種類が制限されます。</span><span class="sxs-lookup"><span data-stu-id="25b5b-391">This limits the kinds of encryption that can be used.</span></span> <span data-ttu-id="25b5b-392">この制限を解除するには、 [GitHub の問題](https://github.com/dotnet/efcore/issues/12205) に投票 #11597。</span><span class="sxs-lookup"><span data-stu-id="25b5b-392">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) to have this limitation removed.</span></span>

> [!WARNING]
> <span data-ttu-id="25b5b-393">独自の暗号化をロールして機密データを保護する場合は、すべての影響を理解してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-393">Make sure to understand all the implications if you roll your own encryption to protect sensitive data.</span></span> <span data-ttu-id="25b5b-394">代わりに、事前構築された暗号化メカニズム (SQL Server での [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) など) を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="25b5b-394">Consider instead using pre-built encryption mechanisms, such as [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) on SQL Server.</span></span>

## <a name="limitations"></a><span data-ttu-id="25b5b-395">制限事項</span><span class="sxs-lookup"><span data-stu-id="25b5b-395">Limitations</span></span>

<span data-ttu-id="25b5b-396">値変換システムには、現在、いくつかの既知の制限があります。</span><span class="sxs-lookup"><span data-stu-id="25b5b-396">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="25b5b-397">現時点では、特定の型のすべてのプロパティで同じ値コンバーターを使用する必要があることを1つの場所で指定する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-397">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="25b5b-398">👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/10784)が必要な場合は、() に投票してください #10784。</span><span class="sxs-lookup"><span data-stu-id="25b5b-398">Please vote (👍) for [GitHub issue #10784](https://github.com/dotnet/efcore/issues/10784) if this is something you need.</span></span>
* <span data-ttu-id="25b5b-399">前述のように、を `null` 変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-399">As noted above, `null` cannot be converted.</span></span> <span data-ttu-id="25b5b-400">👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/13850)が必要な場合は、() に投票してください #13850。</span><span class="sxs-lookup"><span data-stu-id="25b5b-400">Please vote (👍) for [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) if this is something you need.</span></span>
* <span data-ttu-id="25b5b-401">現在、1つのプロパティから複数の列への変換、またはその逆の変換を行う方法はありません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-401">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span> <span data-ttu-id="25b5b-402">👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/13947)が必要な場合は、() に投票してください #13947。</span><span class="sxs-lookup"><span data-stu-id="25b5b-402">Please vote (👍) for [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947) if this is something you need.</span></span>
* <span data-ttu-id="25b5b-403">値の生成は、値コンバーターによってマップされるほとんどのキーではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-403">Value generation is not supported for most keys mapped through value converters.</span></span> <span data-ttu-id="25b5b-404">👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/11597)が必要な場合は、() に投票してください #11597。</span><span class="sxs-lookup"><span data-stu-id="25b5b-404">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) if this is something you need.</span></span>
* <span data-ttu-id="25b5b-405">値の変換では、現在の DbContext インスタンスを参照できません。</span><span class="sxs-lookup"><span data-stu-id="25b5b-405">Value conversions cannot reference the current DbContext instance.</span></span> <span data-ttu-id="25b5b-406">👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/12205)が必要な場合は、() に投票してください #11597。</span><span class="sxs-lookup"><span data-stu-id="25b5b-406">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) if this is something you need.</span></span>

<span data-ttu-id="25b5b-407">これらの制限事項の削除は、今後のリリースで検討されています。</span><span class="sxs-lookup"><span data-stu-id="25b5b-407">Removal of these limitations is being considered for future releases.</span></span>
