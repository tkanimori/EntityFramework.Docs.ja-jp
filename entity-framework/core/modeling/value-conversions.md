---
title: 値の変換-EF Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 93774bc1bc3887f982faeac151825a6643c1107c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654792"
---
# <a name="value-conversions"></a>値変換

> [!NOTE]  
> これは EF Core 2.1 の新機能です。

値コンバーターを使用すると、データベースに対して読み取りまたは書き込みを行うときに、プロパティ値を変換できます。 この変換は、1つの値から同じ型の別の型 (文字列の暗号化など) にすることも、1つの型の値から別の型の値に変換することもできます (たとえば、列挙値をデータベース内の文字列に変換するなど)。

## <a name="fundamentals"></a>Fundamentals

値コンバーターは、`ModelClrType` と `ProviderClrType`の観点から指定されています。 モデルの種類は、エンティティ型のプロパティの .NET 型です。 プロバイダーの種類は、データベースプロバイダーによって認識される .NET 型です。 たとえば、列挙型を文字列としてデータベースに保存する場合、モデルの種類は列挙型の型であり、プロバイダーの種類は `String`になります。 これらの2つの型は同じにすることができます。

変換は、2つの `Func` 式ツリーを使用して定義されます。1つは `ModelClrType` から `ProviderClrType`、もう一方は `ProviderClrType` から `ModelClrType`です。 式ツリーは、効率的な変換のためにデータベースアクセスコードにコンパイルできるように使用されます。 複雑な変換では、式ツリーは、変換を実行するメソッドを簡単に呼び出すことができます。

## <a name="configuring-a-value-converter"></a>値コンバーターの構成

値の変換は、`DbContext`の `OnModelCreating` のプロパティで定義されます。 たとえば、次のように定義された列挙型とエンティティ型について考えてみます。

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

次に、`OnModelCreating` で変換を定義して、列挙値を文字列として格納することができます (例: "Donkey", "Mule",...)。

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
> `null` 値は、値コンバーターに渡されません。 これにより、変換の実装が容易になり、null 許容のプロパティと null 非許容のプロパティ間で共有できるようになります。

## <a name="the-valueconverter-class"></a>ValueConverter クラス

上記のように `HasConversion` を呼び出すと、`ValueConverter` インスタンスが作成され、プロパティに設定されます。 代わりに、`ValueConverter` を明示的に作成できます。 (例:

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

これは、複数のプロパティで同じ変換を使用する場合に便利です。

> [!NOTE]  
> 現時点では、特定の型のすべてのプロパティで同じ値コンバーターを使用する必要があることを1つの場所で指定する方法はありません。 この機能は、将来のリリースで考慮されます。

## <a name="built-in-converters"></a>組み込みのコンバーター

EF Core には、`Microsoft.EntityFrameworkCore.Storage.ValueConversion` 名前空間に存在する定義済みの `ValueConverter` クラスのセットが付属しています。 これらの数値は、次のとおりです。

* `BoolToZeroOneConverter`-Bool を0に、1を1にします。
* `BoolToStringConverter`-Bool to string ("Y"、"N" など)
* `BoolToTwoValuesConverter`-Bool を任意の2つの値に
* Base64 でエンコードされた文字列への `BytesToStringConverter` バイト配列
* `CastingConverter`-型キャストのみを必要とする変換
* 1文字の文字列に `CharToStringConverter`-Char
* `DateTimeOffsetToBinaryConverter`-DateTimeOffset からバイナリエンコード64ビット値へ
* `DateTimeOffsetToBytesConverter`-DateTimeOffset から byte 配列
* `DateTimeOffsetToStringConverter`-DateTimeOffset to string
* `DateTimeToBinaryConverter`-DateTime ~ 64-Datetimekind.utc を含むビット値
* `DateTimeToStringConverter`-DateTime to string
* `DateTimeToTicksConverter`-DateTime から ticks
* `EnumToNumberConverter`-基になる数値に列挙される
* `EnumToStringConverter`-列挙型文字列
* `GuidToBytesConverter`-バイト配列への Guid
* `GuidToStringConverter`-文字列への Guid
* `NumberToBytesConverter`-バイト配列に対する任意の数値
* `NumberToStringConverter`-文字列に対する任意の数値
* `StringToBytesConverter`-文字列を UTF8 バイトに
* `TimeSpanToStringConverter`-TimeSpan to string
* `TimeSpanToTicksConverter`-TimeSpan から ticks

この一覧に `EnumToStringConverter` が含まれていることに注意してください。 これは、上記のように、明示的に変換を指定する必要がないことを意味します。 代わりに、組み込みのコンバーターを使用します。

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

組み込みのコンバーターはすべてステートレスであるため、1つのインスタンスを複数のプロパティで安全に共有できます。

## <a name="pre-defined-conversions"></a>定義済みの変換

組み込みのコンバーターが存在する一般的な変換では、コンバーターを明示的に指定する必要はありません。 代わりに、使用するプロバイダーの種類を構成するだけで、EF は適切な組み込みのコンバーターを自動的に使用します。 文字列変換の列挙型は上記の例として使用されますが、プロバイダーの種類が構成されている場合、EF は実際にこれを自動的に実行します。

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

列の型を明示的に指定することで、同じことを実現できます。 たとえば、エンティティ型が次のように定義されているとします。

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

その後、列挙値は、`OnModelCreating`で追加の構成を行わなくても、データベースに文字列として保存されます。

## <a name="limitations"></a>制限事項

値変換システムには、現在、いくつかの既知の制限があります。

* 前述のように、`null` を変換することはできません。
* 現在、1つのプロパティから複数の列への変換、またはその逆の変換を行う方法はありません。
* 値の変換を使用すると、式を SQL に変換する EF Core の機能に影響を与える可能性があります。 このような場合、警告がログに記録されます。
これらの制限事項の削除は、将来のリリースで検討されています。
