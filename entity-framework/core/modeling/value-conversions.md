---
title: 値の変換の EF コア
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 329d2757059462468ca30772d37789343c03ba7b
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="value-conversions"></a>値の変換

> [!NOTE]  
> この機能は、EF コア 2.1 の新機能です。

値コンバーターは、読み取りまたはデータベースへの書き込み時に変換するプロパティの値を使用します。 この変換は、同じ型 (文字列の暗号化など) の間の 1 つの値とは別の型 (たとえばへの変換列挙型の値を文字列との間、データベース内) の値に 1 つの型の値から指定できます。

## <a name="fundamentals"></a>Fundamentals

観点で値コンバーターが指定されて、`ModelClrType`と`ProviderClrType`です。 モデルの種類は、.NET 型のエンティティ型のプロパティです。 プロバイダーの種類では、データベース プロバイダーで認識される .NET 型です。 たとえば、モデルの種類は、列挙型の型、列挙型として、データベース内の文字列を保存して、プロバイダーの種類が`String`です。 これら 2 つの型と同じことができます。

使用して 2 つの変換が定義された`Func`式ツリー: から 1 つ`ModelClrType`に`ProviderClrType`およびから、その他の`ProviderClrType`に`ModelClrType`です。 式ツリーを使用するは、効率的な変換のデータベース アクセス コードにコンパイルするようにします。 複雑な変換では、式ツリーを行うには、単純なメソッドの呼び出しを変換を実行する可能性があります。

## <a name="configuring-a-value-converter"></a>値コンバーターを構成します。

値の変換は、プロパティ、DbContext の OnModelCreating で定義されます。 たとえば、として定義されている列挙型とエンティティの種類があるとします。
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
(例: enum 値を文字列として格納する OnModelCreating で変換を定義することができますし、「ロバ」、「ラバ」...)データベース。
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
> A`null`値は、値コンバーターに渡されることはありません。 これは、変換の実装が容易にし、null 値と null 非許容のプロパティの間で共有することができます。

## <a name="the-valueconverter-class"></a>ValueConverter クラス

呼び出す`HasConversion`上記のように作成されます、`ValueConverter`インスタンスし、プロパティに設定します。 `ValueConverter`代わりに明示的に作成することができます。 例えば:
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
これは、複数のプロパティは、同じ変換を使用するときに役立ちます。

> [!NOTE]  
> 現在、1 か所で指定された型のすべてのプロパティが同じ値コンバーターを使用する必要がありますを指定する方法はありません。 この機能は、今後のリリースで見なされます。

## <a name="built-in-converters"></a>組み込みのコンバーター

EF コアを同梱し、一連の定義済み`ValueConverter`で見つかった、クラス、`Microsoft.EntityFrameworkCore.Storage.Converters`名前空間。 これらの数値は、次のとおりです。
* `BoolToZeroOneConverter` -0 と 1 つに Bool
* `BoolToStringConverter` -"Y"および"N"などの文字列をブール値
* `BoolToTwoValuesConverter` 2 つの値をブール値
* `BytesToStringConverter` Base64 でエンコードされた文字列をバイト配列
* `CastingConverter` 変換を行う Csharp のキャストのみが必要です。
* `CharToStringConverter` -単一の文字の文字列に文字します。
* `DateTimeOffsetToBinaryConverter` -64 ビットのバイナリ エンコードされた値を DateTimeOffset
* `DateTimeOffsetToBytesConverter` 、バイト配列に DateTimeOffset
* `DateTimeOffsetToStringConverter` 、文字列に DateTimeOffset
* `DateTimeToBinaryConverter` -DateTime DateTimeKind を含む 64 ビット値
* `DateTimeToStringConverter` -DateTime string から
* `DateTimeToTicksConverter` タイマー刻みに DateTime
* `EnumToNumberConverter` -基になる数列挙型
* `EnumToStringConverter` -列挙型を型の文字列
* `GuidToBytesConverter` のバイト配列へ Guid
* `GuidToStringConverter` -Guid 文字列を
* `NumberToBytesConverter` のバイト配列へ任意の数値
* `NumberToStringConverter` -任意の数値の文字列に
* `StringToBytesConverter` -UTF8 バイトに文字列です。
* `TimeSpanToStringConverter` -文字列を TimeSpan
* `TimeSpanToTicksConverter` -する期間をティック

注意して`EnumToStringConverter`この一覧に含まれています。 これは、変換を上記のように、明示的に指定する必要がないことを意味します。 代わりに、組み込みのコンバーターだけを使用します。
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
組み込みのすべてのコンバーターがステートレスであるため 1 つのインスタンスに安全に共有できる複数のプロパティでことに注意してください。

## <a name="pre-defined-conversions"></a>定義済みの変換

組み込みのコンバーターが存在する共通の変換には、コンバーターを明示的に指定する必要はありません。 代わりに、構成するプロバイダーの種類を使用する必要があり、EF は自動的に適切なビルドでコンバーターを使用します。 列挙型文字列への変換からは、上記の例として使用されますが、EF は実際には、この処理に自動的にプロバイダーの種類が構成されている場合。
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
列のデータ型を明示的に指定して、同じことを実現できます。 たとえば、エンティティ型が定義されている場合などのようにします。
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
列挙値は、OnModelCreating でさらに構成をしなくても、データベース内の文字列として保存されます。

## <a name="limitations"></a>制限事項

値の変換システムのいくつかの既知の現在制限があります。
* 前述のよう`null`変換することはできません。
* 現在、1 つのプロパティを複数の列またはその逆の変換を分散する方法はありません。
* 値の変換の使用可能性がある機能に影響の EF コアの式を SQL に変換します。 このような場合に警告が記録されます。
今後のリリースでは、これらの制限の削除を検討しています。
