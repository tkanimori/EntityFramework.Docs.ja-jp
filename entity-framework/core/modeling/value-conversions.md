---
title: 値の変換-EF Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 93774bc1bc3887f982faeac151825a6643c1107c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414555"
---
# <a name="value-conversions"></a><span data-ttu-id="26130-102">値変換</span><span class="sxs-lookup"><span data-stu-id="26130-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="26130-103">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="26130-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="26130-104">値コンバーターを使用すると、データベースに対して読み取りまたは書き込みを行うときに、プロパティ値を変換できます。</span><span class="sxs-lookup"><span data-stu-id="26130-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="26130-105">この変換は、1つの値から同じ型の別の型 (文字列の暗号化など) にすることも、1つの型の値から別の型の値に変換することもできます (たとえば、列挙値をデータベース内の文字列に変換するなど)。</span><span class="sxs-lookup"><span data-stu-id="26130-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="26130-106">基礎</span><span class="sxs-lookup"><span data-stu-id="26130-106">Fundamentals</span></span>

<span data-ttu-id="26130-107">値コンバーターは、`ModelClrType` と `ProviderClrType`の観点から指定されています。</span><span class="sxs-lookup"><span data-stu-id="26130-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="26130-108">モデルの種類は、エンティティ型のプロパティの .NET 型です。</span><span class="sxs-lookup"><span data-stu-id="26130-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="26130-109">プロバイダーの種類は、データベースプロバイダーによって認識される .NET 型です。</span><span class="sxs-lookup"><span data-stu-id="26130-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="26130-110">たとえば、列挙型を文字列としてデータベースに保存する場合、モデルの種類は列挙型の型であり、プロバイダーの種類は `String`になります。</span><span class="sxs-lookup"><span data-stu-id="26130-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="26130-111">これらの2つの型は同じにすることができます。</span><span class="sxs-lookup"><span data-stu-id="26130-111">These two types can be the same.</span></span>

<span data-ttu-id="26130-112">変換は、2つの `Func` 式ツリーを使用して定義されます。1つは `ModelClrType` から `ProviderClrType`、もう一方は `ProviderClrType` から `ModelClrType`です。</span><span class="sxs-lookup"><span data-stu-id="26130-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="26130-113">式ツリーは、効率的な変換のためにデータベースアクセスコードにコンパイルできるように使用されます。</span><span class="sxs-lookup"><span data-stu-id="26130-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="26130-114">複雑な変換では、式ツリーは、変換を実行するメソッドを簡単に呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="26130-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="26130-115">値コンバーターの構成</span><span class="sxs-lookup"><span data-stu-id="26130-115">Configuring a value converter</span></span>

<span data-ttu-id="26130-116">値の変換は、`DbContext`の `OnModelCreating` のプロパティで定義されます。</span><span class="sxs-lookup"><span data-stu-id="26130-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="26130-117">たとえば、次のように定義された列挙型とエンティティ型について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="26130-117">For example, consider an enum and entity type defined as:</span></span>

``` csharp
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
```

<span data-ttu-id="26130-118">次に、`OnModelCreating` で変換を定義して、列挙値を文字列として格納することができます (例: "Donkey", "Mule",...)。</span><span class="sxs-lookup"><span data-stu-id="26130-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> <span data-ttu-id="26130-119">`null` 値は、値コンバーターに渡されません。</span><span class="sxs-lookup"><span data-stu-id="26130-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="26130-120">これにより、変換の実装が容易になり、null 許容のプロパティと null 非許容のプロパティ間で共有できるようになります。</span><span class="sxs-lookup"><span data-stu-id="26130-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="26130-121">ValueConverter クラス</span><span class="sxs-lookup"><span data-stu-id="26130-121">The ValueConverter class</span></span>

<span data-ttu-id="26130-122">上記のように `HasConversion` を呼び出すと、`ValueConverter` インスタンスが作成され、プロパティに設定されます。</span><span class="sxs-lookup"><span data-stu-id="26130-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="26130-123">代わりに、`ValueConverter` を明示的に作成できます。</span><span class="sxs-lookup"><span data-stu-id="26130-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="26130-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="26130-124">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="26130-125">これは、複数のプロパティで同じ変換を使用する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="26130-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="26130-126">現時点では、特定の型のすべてのプロパティで同じ値コンバーターを使用する必要があることを1つの場所で指定する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="26130-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="26130-127">この機能は、将来のリリースで考慮されます。</span><span class="sxs-lookup"><span data-stu-id="26130-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="26130-128">組み込みのコンバーター</span><span class="sxs-lookup"><span data-stu-id="26130-128">Built-in converters</span></span>

<span data-ttu-id="26130-129">EF Core には、`Microsoft.EntityFrameworkCore.Storage.ValueConversion` 名前空間に存在する定義済みの `ValueConverter` クラスのセットが付属しています。</span><span class="sxs-lookup"><span data-stu-id="26130-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="26130-130">次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="26130-130">These are:</span></span>

* <span data-ttu-id="26130-131">`BoolToZeroOneConverter`-Bool を0に、1を1にします。</span><span class="sxs-lookup"><span data-stu-id="26130-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="26130-132">`BoolToStringConverter`-Bool to string ("Y"、"N" など)</span><span class="sxs-lookup"><span data-stu-id="26130-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="26130-133">`BoolToTwoValuesConverter`-Bool を任意の2つの値に</span><span class="sxs-lookup"><span data-stu-id="26130-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="26130-134">Base64 でエンコードされた文字列への `BytesToStringConverter` バイト配列</span><span class="sxs-lookup"><span data-stu-id="26130-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="26130-135">`CastingConverter`-型キャストのみを必要とする変換</span><span class="sxs-lookup"><span data-stu-id="26130-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="26130-136">1文字の文字列に `CharToStringConverter`-Char</span><span class="sxs-lookup"><span data-stu-id="26130-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="26130-137">`DateTimeOffsetToBinaryConverter`-DateTimeOffset からバイナリエンコード64ビット値へ</span><span class="sxs-lookup"><span data-stu-id="26130-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="26130-138">`DateTimeOffsetToBytesConverter`-DateTimeOffset から byte 配列</span><span class="sxs-lookup"><span data-stu-id="26130-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="26130-139">`DateTimeOffsetToStringConverter`-DateTimeOffset to string</span><span class="sxs-lookup"><span data-stu-id="26130-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="26130-140">`DateTimeToBinaryConverter`-DateTime ~ 64-Datetimekind.utc を含むビット値</span><span class="sxs-lookup"><span data-stu-id="26130-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="26130-141">`DateTimeToStringConverter`-DateTime to string</span><span class="sxs-lookup"><span data-stu-id="26130-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="26130-142">`DateTimeToTicksConverter`-DateTime から ticks</span><span class="sxs-lookup"><span data-stu-id="26130-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="26130-143">`EnumToNumberConverter`-基になる数値に列挙される</span><span class="sxs-lookup"><span data-stu-id="26130-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="26130-144">`EnumToStringConverter`-列挙型文字列</span><span class="sxs-lookup"><span data-stu-id="26130-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="26130-145">`GuidToBytesConverter`-バイト配列への Guid</span><span class="sxs-lookup"><span data-stu-id="26130-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="26130-146">`GuidToStringConverter`-文字列への Guid</span><span class="sxs-lookup"><span data-stu-id="26130-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="26130-147">`NumberToBytesConverter`-バイト配列に対する任意の数値</span><span class="sxs-lookup"><span data-stu-id="26130-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="26130-148">`NumberToStringConverter`-文字列に対する任意の数値</span><span class="sxs-lookup"><span data-stu-id="26130-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="26130-149">`StringToBytesConverter`-文字列を UTF8 バイトに</span><span class="sxs-lookup"><span data-stu-id="26130-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="26130-150">`TimeSpanToStringConverter`-TimeSpan to string</span><span class="sxs-lookup"><span data-stu-id="26130-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="26130-151">`TimeSpanToTicksConverter`-TimeSpan から ticks</span><span class="sxs-lookup"><span data-stu-id="26130-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="26130-152">この一覧に `EnumToStringConverter` が含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="26130-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="26130-153">これは、上記のように、明示的に変換を指定する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="26130-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="26130-154">代わりに、組み込みのコンバーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="26130-154">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="26130-155">組み込みのコンバーターはすべてステートレスであるため、1つのインスタンスを複数のプロパティで安全に共有できます。</span><span class="sxs-lookup"><span data-stu-id="26130-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="26130-156">定義済みの変換</span><span class="sxs-lookup"><span data-stu-id="26130-156">Pre-defined conversions</span></span>

<span data-ttu-id="26130-157">組み込みのコンバーターが存在する一般的な変換では、コンバーターを明示的に指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="26130-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="26130-158">代わりに、使用するプロバイダーの種類を構成するだけで、EF は適切な組み込みのコンバーターを自動的に使用します。</span><span class="sxs-lookup"><span data-stu-id="26130-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="26130-159">文字列変換の列挙型は上記の例として使用されますが、プロバイダーの種類が構成されている場合、EF は実際にこれを自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="26130-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="26130-160">列の型を明示的に指定することで、同じことを実現できます。</span><span class="sxs-lookup"><span data-stu-id="26130-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="26130-161">たとえば、エンティティ型が次のように定義されているとします。</span><span class="sxs-lookup"><span data-stu-id="26130-161">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="26130-162">その後、列挙値は、`OnModelCreating`で追加の構成を行わなくても、データベースに文字列として保存されます。</span><span class="sxs-lookup"><span data-stu-id="26130-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="26130-163">制限事項</span><span class="sxs-lookup"><span data-stu-id="26130-163">Limitations</span></span>

<span data-ttu-id="26130-164">値変換システムには、現在、いくつかの既知の制限があります。</span><span class="sxs-lookup"><span data-stu-id="26130-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="26130-165">前述のように、`null` を変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="26130-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="26130-166">現在、1つのプロパティから複数の列への変換、またはその逆の変換を行う方法はありません。</span><span class="sxs-lookup"><span data-stu-id="26130-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="26130-167">値の変換を使用すると、式を SQL に変換する EF Core の機能に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="26130-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="26130-168">このような場合、警告がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="26130-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="26130-169">これらの制限事項の削除は、将来のリリースで検討されています。</span><span class="sxs-lookup"><span data-stu-id="26130-169">Removal of these limitations is being considered for a future release.</span></span>
