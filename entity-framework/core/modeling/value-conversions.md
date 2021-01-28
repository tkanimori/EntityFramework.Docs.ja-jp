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
# <a name="value-conversions"></a>値変換

値コンバーターを使用すると、データベースに対して読み取りまたは書き込みを行うときに、プロパティ値を変換できます。 この変換は、1つの値から同じ型の別の型 (文字列の暗号化など) にすることも、1つの型の値から別の型の値に変換することもできます (たとえば、列挙値をデータベース内の文字列に変換するなど)。

> [!TIP]
> このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)することで実行およびデバッグできます。

## <a name="overview"></a>概要

値コンバーターは、との観点で指定され `ModelClrType` てい `ProviderClrType` ます。 モデルの種類は、エンティティ型のプロパティの .NET 型です。 プロバイダーの種類は、データベースプロバイダーによって認識される .NET 型です。 たとえば、列挙型を文字列としてデータベースに保存する場合、モデルの種類は列挙型の型であり、プロバイダーの型は `String` です。 これらの2つの型は同じにすることができます。

変換は、からへの変換 `Func` `ModelClrType` `ProviderClrType` と、からへの2つの式ツリーを使用して定義され `ProviderClrType` `ModelClrType` ます。 式ツリーは、効率的な変換のためにデータベースアクセスデリゲートにコンパイルできるように使用されます。 式ツリーには、複雑な変換のための変換メソッドの単純な呼び出しを含めることができます。

> [!NOTE]
> 値の変換用に構成されたプロパティは、も指定する必要があり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> ます。 詳細については、次の例と [値の比較](xref:core/modeling/value-comparers) 子に関するドキュメントを参照してください。

## <a name="configuring-a-value-converter"></a>値コンバーターの構成

値の変換はで構成され <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> ます。 たとえば、次のように定義された列挙型とエンティティ型について考えてみます。

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

では、 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 列挙値を "Donkey"、"Mule" などの文字列としてデータベースに格納するようにを構成できます。また、からに変換する1つの関数を指定するだけで、 `ModelClrType` 逆の `ProviderClrType` 変換については、次のような変換を行う必要があります。

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
> 値は、 `null` 値コンバーターに渡されません。 データベース列の null は、常にエンティティインスタンス内の null になります。また、その逆も同様です。 これにより、変換の実装が容易になり、null 許容のプロパティと null 非許容のプロパティ間で共有できるようになります。 詳細については、 [GitHub の問題 #13850](https://github.com/dotnet/efcore/issues/13850) を参照してください。

## <a name="pre-defined-conversions"></a>定義済みの変換

EF Core には、変換関数を手動で記述する必要がないように定義済みの変換が多数含まれています。 代わりに、EF Core は、モデルのプロパティの型および要求されたデータベースプロバイダーの種類に基づいて、使用する変換を選択します。

たとえば、文字列の列挙型への変換は上の例として使用されますが、実際には、プロバイダーの種類が `string` のジェネリック型を使用するように構成されている場合に、EF Core によって自動的に実行され <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> ます。

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

データベース列の型を明示的に指定することで、同じことを実現できます。 たとえば、エンティティ型が次のように定義されているとします。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

その後、列挙値は、で追加の構成を行わなくても、データベースに文字列として保存され <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ます。

## <a name="the-valueconverter-class"></a>ValueConverter クラス

<xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>上に示すようにを呼び出すと、インスタンスが作成され <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> 、プロパティに設定されます。 代わりに、を `ValueConverter` 明示的に作成できます。 例:

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

これは、複数のプロパティで同じ変換を使用する場合に便利です。

## <a name="built-in-converters"></a>組み込みのコンバーター

前述のように、EF Core には、名前空間で定義されている一連の定義済みクラスが付属して <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> います。 多くの場合、EF は、前述のように、モデルのプロパティの型とデータベースで要求された型に基づいて、適切な組み込みのコンバーターを選択します。 たとえば、プロパティでを使用すると、 `.HasConversion<int>()` `bool` EF Core によってブール値が0と1の数値に変換されます。

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

これは、組み込みのインスタンスを作成し、それを明示的に設定することと同じです <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> 。

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

次の表は、一般的に使用される定義済みのモデル/プロパティ型からデータベースプロバイダーの型への変換をまとめたものです。 テーブルでは、、、、、、、、、、、、 `any_numeric_type` またはのいずれか `int` を意味 `short` `long` `byte` `uint` `ushort` `ulong` `sbyte` `char` `decimal` `float` `double` します。

| モデル/プロパティの型 | プロバイダー/データベースの種類 | 変換                                                | 使用法
|:--------------------|------------------------|-----------------------------------------------------------|------
| [bool]                | any_numeric_type       | False/true から0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | 任意の2つの数値に対する False/true                             | <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> を使用します
|                     | string                 | False/true から "Y"/"N" へ                                     | `.HasConversion<string>()`
|                     | string                 | 任意の2つの文字列に対する False/true                             | <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> を使用します
| any_numeric_type    | [bool]                   | 0/1 から false/true へ                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | 単純なキャスト                                               | `.HasConversion<any_numeric_type>()`
|                     | string                 | 数値を文字列として指定します。                                    | `.HasConversion<string>()`
| 列挙型                | any_numeric_type       | 列挙型の数値                             | `.HasConversion<any_numeric_type>()`
|                     | string                 | 列挙値の文字列表現。               | `.HasConversion<string>()`
| string              | [bool]                   | 文字列をブール値として解析します。                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | 文字列を指定された数値型として解析します。               | `.HasConversion<any_numeric_type>()`
|                     | char                   | 文字列の最初の文字                         | `.HasConversion<char>()`
|                     | DateTime               | 文字列を DateTime として解析します。                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | 文字列を DateTimeOffset として解析します。                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | 文字列を TimeSpan として解析します。                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | 文字列を Guid として解析します。                               | `.HasConversion<Guid>()`
|                     | byte[]                 | 文字列を UTF8 バイトとして指定します。                                  | `.HasConversion<byte[]>()`
| char                | string                 | 1つの文字列                                 | `.HasConversion<string>()`
| DateTime            | long                   | 日付/時刻を保持する、DateTime. Kind                | `.HasConversion<long>()`
|                     | long                   | クロック                                                     | <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> を使用します
|                     | string                 | インバリアントカルチャの日付/時刻文字列                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | オフセット付きのエンコードされた日付/時刻                             | `.HasConversion<long>()`
|                     | string                 | オフセットを含むインバリアントカルチャの日付/時刻文字列            | `.HasConversion<string>()`
| TimeSpan            | long                   | クロック                                                     | `.HasConversion<long>()`
|                     | string                 | インバリアントカルチャの期間文字列                        | `.HasConversion<string>()`
| Uri                 | string                 | URI を文字列として指定します。                                       | `.HasConversion<string>()`
| PhysicalAddress     | string                 | アドレスが文字列として指定されています。                                   | `.HasConversion<string>()`
|                     | byte[]                 | ビッグエンディアンネットワークの順序のバイト数                         | `.HasConversion<byte[]>()`
| IPAddress           | string                 | アドレスが文字列として指定されています。                                   | `.HasConversion<string>()`
|                     | byte[]                 | ビッグエンディアンネットワークの順序のバイト数                         | `.HasConversion<byte[]>()`
| Guid                | string                 | ' Dddddddd-dddd-dddd-dddd-dddddddddddd ' 形式の GUID | `.HasConversion<string>()`
|                     | byte[]                 | .NET バイナリシリアル化の順序のバイト数                  | `.HasConversion<byte[]>()`

これらの変換では、値の形式が変換に適していると想定しています。 たとえば、文字列値を数値として解析できない場合、文字列を数値に変換しても失敗します。

組み込みのコンバーターの完全な一覧は次のとおりです。

* ブール型プロパティの変換:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool to string ("Y"、"N" など)
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool から任意の2つの値へ
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool から0および1
* バイト配列プロパティの変換:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Base64 でエンコードされた文字列へのバイト配列
* 型キャストのみを必要とする変換
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -型キャストのみを必要とする変換
* Char プロパティの変換:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char から単一の文字列へ
* プロパティの変換 <xref:System.DateTimeOffset> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> バイナリエンコードされた64ビット値に
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> バイト配列への
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> 文字列に
* プロパティの変換 <xref:System.DateTime> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> Datetimekind.utc を含む64ビット値に
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> 文字列に
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> ティックに
* 列挙型プロパティの変換:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -基になる数値の列挙型
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -文字列への列挙
* プロパティの変換 <xref:System.Guid> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> バイト配列への
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> 文字列に
* プロパティの変換 <xref:System.Net.IPAddress> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> バイト配列への
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> 文字列に
* 数値 (int、double、decimal など) のプロパティを変換します。
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -バイト配列に対する任意の数値
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -文字列に対する任意の数値
* プロパティの変換 <xref:System.Net.NetworkInformation.PhysicalAddress> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> バイト配列への
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> 文字列に
* 文字列プロパティの変換:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -"Y" や "N" などの文字列を bool に
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -文字列から UTF8 バイトへ
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -文字列から文字へ
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -文字列から <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -文字列から <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -列挙する文字列
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -文字列から <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -文字列から数値型
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -文字列から <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -文字列から <xref:System.Uri>
* プロパティの変換 <xref:System.TimeSpan> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> 文字列に
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> ティックに
* プロパティの変換 <xref:System.Uri> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> 文字列に

組み込みのコンバーターはすべてステートレスであるため、1つのインスタンスを複数のプロパティで安全に共有できます。

## <a name="column-facets-and-mapping-hints"></a>列のファセットとマッピングヒント

一部のデータベースの種類には、データの格納方法を変更するファセットがあります。 次の設定があります。

* Decimal および date/time 列の有効桁数と小数点以下桁数
* バイナリおよび文字列型の列のサイズ/長さ
* 文字列型の列の場合は Unicode

これらのファセットは、値コンバーターを使用するプロパティの通常の方法で構成でき、変換後のデータベース型に適用されます。 たとえば、列挙型から文字列への変換では、データベース列が非 Unicode で、最大20文字を格納するように指定できます。

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

または、コンバーターを明示的に作成する場合は、次のようにします。

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

この結果、 `varchar(20)` SQL Server に対して EF Core 移行を使用すると、列が表示されるようになります。

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

ただし、既定ですべての列がである場合は、 `EquineBeast` `varchar(20)` この情報をとして値コンバーターに与えることができ <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> ます。 例:

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

このコンバーターが使用されると、データベース列は unicode 以外の値になり、最大長は20になります。 ただし、これらは、マップされたプロパティに明示的に設定されているファセットによってオーバーライドされるため、ヒントにすぎません。

## <a name="examples"></a>例

### <a name="simple-value-objects"></a>単純値オブジェクト

この例では、単純型を使用してプリミティブ型をラップします。 これは、モデル内の型をプリミティブ型よりも具体的な (つまり、よりタイプセーフな) 必要がある場合に便利です。 この例では、この型は `Dollars` で、10進プリミティブをラップします。

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

これは、エンティティ型で使用できます。

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

データベースに格納されている場合は、基になるに変換 `decimal` されます。

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> この値オブジェクトは、 [readonly 構造体](/dotnet/csharp/language-reference/builtin-types/struct)として実装されます。 これは、EF Core が問題なくスナップショットして値を比較できることを意味します。 詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。

### <a name="composite-value-objects"></a>複合値オブジェクト

前の例では、値オブジェクトの型には1つのプロパティのみが含まれていました。 値オブジェクト型では、ドメインの概念を形成する複数のプロパティを構成するのが一般的です。 たとえば、金額と通貨の両方を含む一般的な型は次のように `Money` なります。

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

この値オブジェクトは、次のようにエンティティ型で使用できます。

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

現在、値コンバーターでは、単一のデータベース列との間でのみ値を変換できます。 この制限は、オブジェクトのすべてのプロパティ値を1つの列値にエンコードする必要があることを意味します。 通常、この処理は、データベースへの移動時にオブジェクトをシリアル化し、その後で逆シリアル化することによって処理されます。たとえば、次のように使用し <xref:System.Text.Json> ます。

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> EF Core 6.0 では、オブジェクトを複数の列にマップできるようにすることを計画しています。これにより、シリアル化を使用する必要がなくなります。 これは、 [GitHub の問題 #13947](https://github.com/dotnet/efcore/issues/13947)によって追跡されます。

> [!NOTE]
> 前の例と同様に、この値オブジェクトは [readonly 構造体](/dotnet/csharp/language-reference/builtin-types/struct)として実装されます。 これは、EF Core が問題なくスナップショットして値を比較できることを意味します。 詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。

### <a name="collections-of-primitives"></a>プリミティブのコレクション

シリアル化を使用して、プリミティブ値のコレクションを格納することもできます。 例:

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

もう一度を使用する <xref:System.Text.Json> :

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

`ICollection<string>` 変更可能な参照型を表します。 これは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> EF Core が変更を追跡して正しく検出できるように、が必要であることを意味します。 詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。

### <a name="collections-of-value-objects"></a>値オブジェクトのコレクション

前の2つの例を組み合わせて、値オブジェクトのコレクションを作成できます。 たとえば、 `AnnualFinance` 1 年間、ブログの金融をモデル化する型について考えてみましょう。

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

この型は、前に作成したいくつかの型を結合し `Money` ます。

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

次に、のコレクションを `AnnualFinance` エンティティ型に追加できます。

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

次に、シリアル化を使用して次のものを格納します。

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
> 以前と同様に、この変換にはが必要です <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 。 詳細については、「 [値の比較](xref:core/modeling/value-comparers) 子」を参照してください。

### <a name="value-objects-as-keys"></a>キーとしての値オブジェクト

値を割り当てるときにタイプセーフレベルを追加するために、プリミティブキープロパティを値オブジェクトにラップすることがあります。 たとえば、ブログのキーの種類と、投稿用のキーの種類を実装できます。

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

これらは、ドメインモデルで使用できます。

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

は誤ってに割り当てられず、誤ってに割り当てられないことに注意 `Blog.Id` `PostKey` して `Post.Id` `BlogKey` ください。 同様に、 `Post.BlogId` 外部キープロパティにはが割り当てられている必要があり `BlogKey` ます。

> [!NOTE]
> このパターンを表示することは、お勧めしません。 このレベルの抽象化が開発環境を支援または hampering しているかどうかを慎重に検討してください。 また、キー値を直接処理するのではなく、ナビゲーションと生成されたキーの使用を検討してください。

これらのキープロパティは、値コンバーターを使用してマップできます。

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
> 現在、変換のキープロパティは、生成されたキー値を使用できません。 この制限を解除するには、 [GitHub の問題](https://github.com/dotnet/efcore/issues/11597) に投票 #11597。

### <a name="use-ulong-for-timestamprowversion"></a>Timestamp/rowversion に ulong を使用する

SQL Server は[、8バイトのバイナリ `rowversion` / `timestamp` 列](/sql/t-sql/data-types/rowversion-transact-sql)を使用した自動[オプティミスティック同時実行制御](xref:core/saving/concurrency)をサポートしています。 これらは常に、8バイトの配列を使用してデータベースに対して読み書きされます。 ただし、バイト配列は変更可能な参照型であるため、処理が多少困難になります。 値コンバーターを使用すると、 `rowversion` をプロパティに割り当てること `ulong` ができます。これは、バイト配列よりも適切で使いやすくなります。 たとえば、 `Blog` ulong 同時実行トークンを持つエンティティについて考えてみます。

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

`rowversion`値コンバーターを使用して SQL server 列にマップできます。

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>日付を読み取るときに DateTime を指定します。

SQL Server <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> は、を <xref:System.DateTime> またはとして格納するときに、フラグを破棄し [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) ます。 つまり、データベースから返される DateTime 値には、常に <xref:System.DateTimeKind> のが含ま `Unspecified` れます。

値コンバーターは、次の2つの方法で使用できます。 まず、EF Core には、フラグを保持する8バイトの不透明な値を作成する値コンバーターがあり `Kind` ます。 例:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

これにより、異なるフラグを持つ DateTime 値 `Kind` をデータベースに混在させることができます。

この方法の問題は、データベースで認識できる `datetime` 列または列がなくなったことです `datetime2` 。 そのため、通常は UTC 時刻 (または、通常はあまりローカル時刻) を格納し、次にフラグを無視するか、 `Kind` 値コンバーターを使用して適切な値に設定します。 たとえば、次のコンバーターを使用すると、 `DateTime` データベースから読み取られた値が次のようになり <xref:System.DateTimeKind> `UTC` ます。

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

ローカルと UTC の両方の値がエンティティインスタンスで設定されている場合は、挿入前にコンバーターを使用して適切に変換できます。 例:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> 常に UTC 時間を使用するようにすべてのデータベースアクセスコードを統合することを検討してください。ユーザーにデータを表示するときは、現地時間だけを扱います。

### <a name="use-case-insensitive-string-keys"></a>大文字と小文字を区別しない文字列キーを使用する

SQL Server を含む一部のデータベースでは、既定で大文字と小文字を区別しない文字列比較が実行されます。 一方、.NET では、既定で大文字と小文字を区別した文字列比較が実行されます。 つまり、"DotNet" のような外部キー値は SQL Server の主キー値 "DotNet" と一致しますが、EF Core では一致しません。 キーの値の比較子を使用すると、データベースのように大文字と小文字を区別せずに文字列を比較することができ EF Core ます。 たとえば、次のような文字列キーを持つブログ/投稿モデルを考えてみます。

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

一部の `Post.BlogId` 値の大文字と小文字が異なる場合、予期したとおりに動作しません。 これによって発生するエラーは、アプリケーションの動作によって異なりますが、通常は [固定](xref:core/change-tracking/relationship-changes) されていないオブジェクトのグラフや、FK 値が間違っているために失敗した更新などが含まれます。 値の比較子を使用して、この問題を修正できます。

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
> .NET の文字列比較とデータベース文字列の比較は、大文字小文字の区別だけではありません。 このパターンは、単純な ASCII キーに対して機能しますが、カルチャ固有の文字を含むキーに対しては失敗する場合があります。 詳細については [、「照合順序と大文字小文字の区別](xref:core/miscellaneous/collations-and-case-sensitivity) 」を参照してください。

### <a name="handle-fixed-length-database-strings"></a>固定長のデータベース文字列を処理する

前の例では、値コンバーターは必要ありませんでした。 ただし、コンバーターは、やなどの固定長のデータベース文字列型に便利 `char(20)` です `nchar(20)` 。 固定長の文字列は、データベースに値が挿入されるたびに、完全な長さに埋め込まれます。 つまり、キー値 "" は、"" `dotnet` としてデータベースから読み取られ `dotnet..............` ます。ここで、は `.` 空白文字を表します。 この場合、埋め込まれていないキー値と正しく比較されません。

値コンバーターを使用して、キー値の読み取り時にパディングをトリミングできます。 これを前の例の値比較子と組み合わせて、固定長の大文字と小文字を区別しない ASCII キーを正しく比較することができます。 例:

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

### <a name="encrypt-property-values"></a>プロパティ値の暗号化

値コンバーターを使用して、データベースに送信する前にプロパティ値を暗号化し、その後で復号化することができます。たとえば、実際の暗号化アルゴリズムの代替として文字列の反転を使用する場合は、次のようにします。

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> 現在の DbContext (またはその他のセッション状態) への参照を値コンバーター内から取得する方法は現在ありません。 これにより、使用できる暗号化の種類が制限されます。 この制限を解除するには、 [GitHub の問題](https://github.com/dotnet/efcore/issues/12205) に投票 #11597。

> [!WARNING]
> 独自の暗号化をロールして機密データを保護する場合は、すべての影響を理解してください。 代わりに、事前構築された暗号化メカニズム (SQL Server での [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) など) を使用することを検討してください。

## <a name="limitations"></a>制限事項

値変換システムには、現在、いくつかの既知の制限があります。

* 現時点では、特定の型のすべてのプロパティで同じ値コンバーターを使用する必要があることを1つの場所で指定する方法はありません。 👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/10784)が必要な場合は、() に投票してください #10784。
* 前述のように、を `null` 変換することはできません。 👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/13850)が必要な場合は、() に投票してください #13850。
* 現在、1つのプロパティから複数の列への変換、またはその逆の変換を行う方法はありません。 👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/13947)が必要な場合は、() に投票してください #13947。
* 値の生成は、値コンバーターによってマップされるほとんどのキーではサポートされていません。 👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/11597)が必要な場合は、() に投票してください #11597。
* 値の変換では、現在の DbContext インスタンスを参照できません。 👍 [GitHub の問題](https://github.com/dotnet/efcore/issues/12205)が必要な場合は、() に投票してください #11597。

これらの制限事項の削除は、今後のリリースで検討されています。
