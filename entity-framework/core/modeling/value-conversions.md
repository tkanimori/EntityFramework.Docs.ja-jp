---
title: 値の変換-EF Core
description: Entity Framework Core モデルでの値コンバーターの構成
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 221560a145fe25c2b7bf094839dd37791bc25955
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063960"
---
# <a name="value-conversions"></a>値変換

値コンバーターを使用すると、データベースに対して読み取りまたは書き込みを行うときに、プロパティ値を変換できます。 この変換は、1つの値から同じ型の別の型 (文字列の暗号化など) にすることも、1つの型の値から別の型の値に変換することもできます (たとえば、列挙値をデータベース内の文字列に変換するなど)。

## <a name="fundamentals"></a>基本

値コンバーターは、との観点で指定され `ModelClrType` てい `ProviderClrType` ます。 モデルの種類は、エンティティ型のプロパティの .NET 型です。 プロバイダーの種類は、データベースプロバイダーによって認識される .NET 型です。 たとえば、列挙型を文字列としてデータベースに保存する場合、モデルの種類は列挙型の型であり、プロバイダーの型は `String` です。 これらの2つの型は同じにすることができます。

変換は、からへの変換 `Func` `ModelClrType` `ProviderClrType` と、からへの2つの式ツリーを使用して定義され `ProviderClrType` `ModelClrType` ます。 式ツリーは、効率的な変換のためにデータベースアクセスコードにコンパイルできるように使用されます。 複雑な変換では、式ツリーは、変換を実行するメソッドを簡単に呼び出すことができます。

## <a name="configuring-a-value-converter"></a>値コンバーターの構成

値の変換は、ののプロパティで定義され `OnModelCreating` `DbContext` ます。 たとえば、次のように定義された列挙型とエンティティ型について考えてみます。

```csharp
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

次に、で変換を定義し `OnModelCreating` て、列挙値を文字列 (たとえば、"Donkey"、"Mule"、...) としてデータベースに格納できます。

```csharp
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
> 値は、 `null` 値コンバーターに渡されません。 これにより、変換の実装が容易になり、null 許容のプロパティと null 非許容のプロパティ間で共有できるようになります。

## <a name="the-valueconverter-class"></a>ValueConverter クラス

`HasConversion`上に示すようにを呼び出すと、インスタンスが作成され `ValueConverter` 、プロパティに設定されます。 代わりに、を `ValueConverter` 明示的に作成できます。 次に例を示します。

```csharp
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

EF Core には、名前空間で定義されている一連の定義済みクラスが付属して `ValueConverter` `Microsoft.EntityFrameworkCore.Storage.ValueConversion` います。 次のとおりです。

* `BoolToZeroOneConverter` -Bool から0および1
* `BoolToStringConverter` -Bool to string ("Y"、"N" など)
* `BoolToTwoValuesConverter` -Bool から任意の2つの値へ
* `BytesToStringConverter` -Base64 でエンコードされた文字列へのバイト配列
* `CastingConverter` -型キャストのみを必要とする変換
* `CharToStringConverter` -Char から単一の文字列へ
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset からバイナリエンコード64ビット値
* `DateTimeOffsetToBytesConverter` -DateTimeOffset からバイト配列
* `DateTimeOffsetToStringConverter` -DateTimeOffset から文字列
* `DateTimeToBinaryConverter` -DateTime から 64-Datetimekind.utc を含むビット値
* `DateTimeToStringConverter` -DateTime から string
* `DateTimeToTicksConverter` -DateTime からティック
* `EnumToNumberConverter` -基になる数値の列挙型
* `EnumToStringConverter` -文字列への列挙
* `GuidToBytesConverter` -Guid からバイト配列
* `GuidToStringConverter` -文字列への Guid
* `NumberToBytesConverter` -バイト配列に対する任意の数値
* `NumberToStringConverter` -文字列に対する任意の数値
* `StringToBytesConverter` -文字列から UTF8 バイトへ
* `TimeSpanToStringConverter` -TimeSpan から string
* `TimeSpanToTicksConverter` -TimeSpan からティック

`EnumToStringConverter`この一覧にはが含まれていることに注意してください。 これは、上記のように、明示的に変換を指定する必要がないことを意味します。 代わりに、組み込みのコンバーターを使用します。

```csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

組み込みのコンバーターはすべてステートレスであるため、1つのインスタンスを複数のプロパティで安全に共有できます。

## <a name="pre-defined-conversions"></a>定義済みの変換

組み込みのコンバーターが存在する一般的な変換では、コンバーターを明示的に指定する必要はありません。 代わりに、使用するプロバイダーの種類を構成するだけで、EF は適切な組み込みのコンバーターを自動的に使用します。 文字列変換の列挙型は上記の例として使用されますが、プロバイダーの種類が構成されている場合、EF は実際にこれを自動的に実行します。

```csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

列の型を明示的に指定することで、同じことを実現できます。 たとえば、エンティティ型が次のように定義されているとします。

```csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

その後、列挙値は、で追加の構成を行わなくても、データベースに文字列として保存され `OnModelCreating` ます。

## <a name="limitations"></a>制限事項

値変換システムには、現在、いくつかの既知の制限があります。

* 前述のように、を `null` 変換することはできません。
* 現在、1つのプロパティから複数の列への変換、またはその逆の変換を行う方法はありません。
* 値の変換を使用すると、式を SQL に変換する EF Core の機能に影響を与える可能性があります。 このような場合、警告がログに記録されます。
これらの制限事項の削除は、将来のリリースで検討されています。
