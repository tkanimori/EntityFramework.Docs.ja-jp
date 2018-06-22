---
title: 値の変換の EF コア
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 3e97c05a87ad9b4817c03f446031ea6c74704f5b
ms.sourcegitcommit: 605e42232854ce44bae09624a6eebc35b8e2473b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34191117"
---
# <a name="value-conversions"></a><span data-ttu-id="b0264-102">値の変換</span><span class="sxs-lookup"><span data-stu-id="b0264-102">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="b0264-103">この機能は、EF コア 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="b0264-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="b0264-104">値コンバーターは、読み取りまたはデータベースへの書き込み時に変換するプロパティの値を使用します。</span><span class="sxs-lookup"><span data-stu-id="b0264-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="b0264-105">この変換は、同じ型 (文字列の暗号化など) の間の 1 つの値とは別の型 (たとえばへの変換列挙型の値を文字列との間、データベース内) の値に 1 つの型の値から指定できます。</span><span class="sxs-lookup"><span data-stu-id="b0264-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="b0264-106">Fundamentals</span><span class="sxs-lookup"><span data-stu-id="b0264-106">Fundamentals</span></span>

<span data-ttu-id="b0264-107">観点で値コンバーターが指定されて、`ModelClrType`と`ProviderClrType`です。</span><span class="sxs-lookup"><span data-stu-id="b0264-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="b0264-108">モデルの種類は、.NET 型のエンティティ型のプロパティです。</span><span class="sxs-lookup"><span data-stu-id="b0264-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="b0264-109">プロバイダーの種類では、データベース プロバイダーで認識される .NET 型です。</span><span class="sxs-lookup"><span data-stu-id="b0264-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="b0264-110">たとえば、モデルの種類は、列挙型の型、列挙型として、データベース内の文字列を保存して、プロバイダーの種類が`String`です。</span><span class="sxs-lookup"><span data-stu-id="b0264-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="b0264-111">これら 2 つの型と同じことができます。</span><span class="sxs-lookup"><span data-stu-id="b0264-111">These two types can be the same.</span></span>

<span data-ttu-id="b0264-112">使用して 2 つの変換が定義された`Func`式ツリー: から 1 つ`ModelClrType`に`ProviderClrType`およびから、その他の`ProviderClrType`に`ModelClrType`です。</span><span class="sxs-lookup"><span data-stu-id="b0264-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="b0264-113">式ツリーを使用するは、効率的な変換のデータベース アクセス コードにコンパイルするようにします。</span><span class="sxs-lookup"><span data-stu-id="b0264-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="b0264-114">複雑な変換では、式ツリーを行うには、単純なメソッドの呼び出しを変換を実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0264-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="b0264-115">値コンバーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="b0264-115">Configuring a value converter</span></span>

<span data-ttu-id="b0264-116">値の変換は、プロパティ、DbContext の OnModelCreating で定義されます。</span><span class="sxs-lookup"><span data-stu-id="b0264-116">Value conversions are defined on properties in the OnModelCreating of your DbContext.</span></span> <span data-ttu-id="b0264-117">たとえば、として定義されている列挙型とエンティティの種類があるとします。</span><span class="sxs-lookup"><span data-stu-id="b0264-117">For example, consider an enum and entity type defined as:</span></span>
```Csharp
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
<span data-ttu-id="b0264-118">(例: enum 値を文字列として格納する OnModelCreating で変換を定義することができますし、「ロバ」、「ラバ」...])データベース。</span><span class="sxs-lookup"><span data-stu-id="b0264-118">Then conversions can be defined in OnModelCreating to store the enum values as strings (e.g. "Donkey", "Mule", ...) in the database:</span></span>
```Csharp
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
> <span data-ttu-id="b0264-119">A`null`値は、値コンバーターに渡されることはありません。</span><span class="sxs-lookup"><span data-stu-id="b0264-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="b0264-120">これは、変換の実装が容易にし、null 値と null 非許容のプロパティの間で共有することができます。</span><span class="sxs-lookup"><span data-stu-id="b0264-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="b0264-121">ValueConverter クラス</span><span class="sxs-lookup"><span data-stu-id="b0264-121">The ValueConverter class</span></span>

<span data-ttu-id="b0264-122">呼び出す`HasConversion`上記のように作成されます、`ValueConverter`インスタンスし、プロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="b0264-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="b0264-123">`ValueConverter`代わりに明示的に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="b0264-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="b0264-124">例:</span><span class="sxs-lookup"><span data-stu-id="b0264-124">For example:</span></span>
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="b0264-125">これは、複数のプロパティは、同じ変換を使用するときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b0264-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="b0264-126">現在、1 か所で指定された型のすべてのプロパティが同じ値コンバーターを使用する必要がありますを指定する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="b0264-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="b0264-127">この機能は、今後のリリースで見なされます。</span><span class="sxs-lookup"><span data-stu-id="b0264-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="b0264-128">組み込みのコンバーター</span><span class="sxs-lookup"><span data-stu-id="b0264-128">Built-in converters</span></span>

<span data-ttu-id="b0264-129">EF コアを同梱し、一連の定義済み`ValueConverter`で見つかった、クラス、`Microsoft.EntityFrameworkCore.Storage.ValueConversion`名前空間。</span><span class="sxs-lookup"><span data-stu-id="b0264-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="b0264-130">これらの数値は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b0264-130">These are:</span></span>
* <span data-ttu-id="b0264-131">`BoolToZeroOneConverter` -0 と 1 つに Bool</span><span class="sxs-lookup"><span data-stu-id="b0264-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="b0264-132">`BoolToStringConverter` -"Y"および"N"などの文字列をブール値</span><span class="sxs-lookup"><span data-stu-id="b0264-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="b0264-133">`BoolToTwoValuesConverter` 2 つの値をブール値</span><span class="sxs-lookup"><span data-stu-id="b0264-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="b0264-134">`BytesToStringConverter` Base64 でエンコードされた文字列をバイト配列</span><span class="sxs-lookup"><span data-stu-id="b0264-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="b0264-135">`CastingConverter` 変換を行う Csharp のキャストのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="b0264-135">`CastingConverter` - Conversions that require only a Csharp cast</span></span>
* <span data-ttu-id="b0264-136">`CharToStringConverter` -単一の文字の文字列に文字します。</span><span class="sxs-lookup"><span data-stu-id="b0264-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="b0264-137">`DateTimeOffsetToBinaryConverter` -64 ビットのバイナリ エンコードされた値を DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b0264-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="b0264-138">`DateTimeOffsetToBytesConverter` 、バイト配列に DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b0264-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="b0264-139">`DateTimeOffsetToStringConverter` 、文字列に DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b0264-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="b0264-140">`DateTimeToBinaryConverter` -DateTime DateTimeKind を含む 64 ビット値</span><span class="sxs-lookup"><span data-stu-id="b0264-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="b0264-141">`DateTimeToStringConverter` -DateTime string から</span><span class="sxs-lookup"><span data-stu-id="b0264-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="b0264-142">`DateTimeToTicksConverter` タイマー刻みに DateTime</span><span class="sxs-lookup"><span data-stu-id="b0264-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="b0264-143">`EnumToNumberConverter` -基になる数列挙型</span><span class="sxs-lookup"><span data-stu-id="b0264-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="b0264-144">`EnumToStringConverter` -列挙型を型の文字列</span><span class="sxs-lookup"><span data-stu-id="b0264-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="b0264-145">`GuidToBytesConverter` のバイト配列へ Guid</span><span class="sxs-lookup"><span data-stu-id="b0264-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="b0264-146">`GuidToStringConverter` -Guid 文字列を</span><span class="sxs-lookup"><span data-stu-id="b0264-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="b0264-147">`NumberToBytesConverter` のバイト配列へ任意の数値</span><span class="sxs-lookup"><span data-stu-id="b0264-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="b0264-148">`NumberToStringConverter` -任意の数値の文字列に</span><span class="sxs-lookup"><span data-stu-id="b0264-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="b0264-149">`StringToBytesConverter` -UTF8 バイトに文字列です。</span><span class="sxs-lookup"><span data-stu-id="b0264-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="b0264-150">`TimeSpanToStringConverter` -文字列を TimeSpan</span><span class="sxs-lookup"><span data-stu-id="b0264-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="b0264-151">`TimeSpanToTicksConverter` -する期間をティック</span><span class="sxs-lookup"><span data-stu-id="b0264-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="b0264-152">注意して`EnumToStringConverter`この一覧に含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0264-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="b0264-153">これは、変換を上記のように、明示的に指定する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b0264-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="b0264-154">代わりに、組み込みのコンバーターだけを使用します。</span><span class="sxs-lookup"><span data-stu-id="b0264-154">Instead, just use the built-in converter:</span></span>
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
<span data-ttu-id="b0264-155">組み込みのすべてのコンバーターがステートレスであるため 1 つのインスタンスに安全に共有できる複数のプロパティでことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b0264-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="b0264-156">定義済みの変換</span><span class="sxs-lookup"><span data-stu-id="b0264-156">Pre-defined conversions</span></span>

<span data-ttu-id="b0264-157">組み込みのコンバーターが存在する共通の変換には、コンバーターを明示的に指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b0264-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="b0264-158">代わりに、構成するプロバイダーの種類を使用する必要があり、EF は自動的に適切なビルドでコンバーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="b0264-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate build-in converter.</span></span> <span data-ttu-id="b0264-159">列挙型文字列への変換からは、上記の例として使用されますが、EF は実際には、この処理に自動的にプロバイダーの種類が構成されている場合。</span><span class="sxs-lookup"><span data-stu-id="b0264-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
<span data-ttu-id="b0264-160">列のデータ型を明示的に指定して、同じことを実現できます。</span><span class="sxs-lookup"><span data-stu-id="b0264-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="b0264-161">たとえば、エンティティ型が定義されている場合などのようにします。</span><span class="sxs-lookup"><span data-stu-id="b0264-161">For example, if the entity type is defined like so:</span></span>
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
<span data-ttu-id="b0264-162">列挙値は、OnModelCreating でさらに構成をしなくても、データベース内の文字列として保存されます。</span><span class="sxs-lookup"><span data-stu-id="b0264-162">Then the enum values will be saved as strings in the database without any further configuration in OnModelCreating.</span></span>

## <a name="limitations"></a><span data-ttu-id="b0264-163">制限事項</span><span class="sxs-lookup"><span data-stu-id="b0264-163">Limitations</span></span>

<span data-ttu-id="b0264-164">値の変換システムのいくつかの既知の現在制限があります。</span><span class="sxs-lookup"><span data-stu-id="b0264-164">There are a few known current limitations of the value convertion system:</span></span>
* <span data-ttu-id="b0264-165">前述のよう`null`変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="b0264-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="b0264-166">現在、1 つのプロパティを複数の列またはその逆の変換を分散する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="b0264-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="b0264-167">値の変換の使用可能性がある機能に影響の EF コアの式を SQL に変換します。</span><span class="sxs-lookup"><span data-stu-id="b0264-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="b0264-168">このような場合に警告が記録されます。</span><span class="sxs-lookup"><span data-stu-id="b0264-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="b0264-169">今後のリリースでは、これらの制限の削除を検討しています。</span><span class="sxs-lookup"><span data-stu-id="b0264-169">Removal of these limitations is being considered for a future release.</span></span>
