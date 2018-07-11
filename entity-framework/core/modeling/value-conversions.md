---
title: 値の変換 - EF Core
author: ajcvickers
ms.author: divega
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/value-conversions
ms.openlocfilehash: 5bfb6111ac450db91f3f1a7074a924a1c8400ce7
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949093"
---
# <a name="value-conversions"></a>値の変換

> [!NOTE]  
> これは EF Core 2.1 の新機能です。

値コンバーターは、プロパティの読み取りまたはデータベースへの書き込み時に変換される値を使用します。 この変換は、同じ型 (文字列の暗号化など) のもう 1 つ 1 つの値または別の型 (たとえば、変換列挙型の値と、データベース内の文字列の間) の値に 1 つの型の値からを指定できます。

## <a name="fundamentals"></a>Fundamentals

観点で値コンバーターが指定されて、`ModelClrType`と`ProviderClrType`します。 モデル型は、エンティティ型のプロパティの .NET 型です。 プロバイダーの種類では、データベース プロバイダーで認識される .NET 型です。 たとえば、モデルの種類は、列挙型の型を列挙型として、データベース内の文字列を保存して、プロバイダーの種類が`String`します。 これら 2 つの型は同じであることができます。

変換が 2 つを使用して定義されている`Func`式ツリー: から 1 つ`ModelClrType`に`ProviderClrType`およびその他から`ProviderClrType`に`ModelClrType`します。 式ツリーは、効率的な変換のデータベース アクセス コードのコンパイルするために使用します。 複雑な変換は、式ツリーに変換を実行するメソッドへの単純な呼び出し可能性があります。

## <a name="configuring-a-value-converter"></a>値コンバーターを構成します。

値の変換は、DbContext の OnModelCreating 内のプロパティで定義されます。 たとえば、として定義された列挙型とエンティティ型があるとします。
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
データベース内の文字列 (たとえば、「ロバ」、「ラバ」、...) として列挙型の値を格納する OnModelCreating で変換を定義できます。
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
> A`null`値が値コンバーターに渡されることはありません。 これは、操作を行うと、変換の実装が簡単、null 許容型と null 非許容のプロパティの間で共有することができます。

## <a name="the-valueconverter-class"></a>ValueConverter クラス

呼び出す`HasConversion`作成は上記を`ValueConverter`インスタンスし、プロパティに設定します。 `ValueConverter`代わりに明示的に作成することができます。 例えば:
```Csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
これは、複数のプロパティが、同じ変換を使用するときに役立ちます。

> [!NOTE]  
> 現在、1 つの場所で指定された型のすべてのプロパティが同じ値コンバーターを使用する必要がありますを指定する方法はありません。 この機能は、今後のリリースで検討されます。

## <a name="built-in-converters"></a>組み込みのコンバーター

定義済みの一連の付属の EF Core`ValueConverter`内で見つかったクラス、`Microsoft.EntityFrameworkCore.Storage.ValueConversion`名前空間。 これらの数値は、次のとおりです。
* `BoolToZeroOneConverter` -0 と 1 つに Bool
* `BoolToStringConverter` -"Y"と"N"などの文字列をブール値
* `BoolToTwoValuesConverter` -2 つの値をブール値
* `BytesToStringConverter` Base64 でエンコードされた文字列をバイト配列
* `CastingConverter` -Csharp のキャストのみが必要です変換
* `CharToStringConverter` -1 つの文字の文字列を文字します。
* `DateTimeOffsetToBinaryConverter` -64 ビットのバイナリ エンコードされた値を DateTimeOffset
* `DateTimeOffsetToBytesConverter` 、バイト配列に DateTimeOffset
* `DateTimeOffsetToStringConverter` -文字列に DateTimeOffset
* `DateTimeToBinaryConverter` の DateTimeKind を含む 64 ビット値 DateTime
* `DateTimeToStringConverter` -文字列を DateTime
* `DateTimeToTicksConverter` タイマー刻みを DateTime
* `EnumToNumberConverter` -基になる数列挙型
* `EnumToStringConverter` -文字列に列挙型
* `GuidToBytesConverter` のバイト配列へ Guid
* `GuidToStringConverter` の Guid 文字列を
* `NumberToBytesConverter` のバイト配列へ任意の数値
* `NumberToStringConverter` -任意の数値の文字列
* `StringToBytesConverter` -UTF8 バイトに文字列です。
* `TimeSpanToStringConverter` -文字列を TimeSpan
* `TimeSpanToTicksConverter` -タイマー刻みを TimeSpan

注意`EnumToStringConverter`この一覧が含まれます。 これは変換を明示的に、上記のように指定する必要がないことを意味します。 代わりに、組み込みのコンバーターを使用します。
```Csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```
組み込みのすべてのコンバーターはステートレスなので 1 つのインスタンスは安全に共有できる複数のプロパティでことに注意してください。

## <a name="pre-defined-conversions"></a>定義済みの変換

組み込みのコンバーターが存在する共通の変換には、コンバーターを明示的に指定する必要はありません。 代わりに、どのプロバイダーの種類を使用する必要がありますを構成し、EF が自動的に適切なビルドでコンバーターを使用します。 列挙型文字列への変換からは、上記の例として使用されますが、EF 実際には、自動的にプロバイダーの種類が構成されている場合。
```Csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```
列の型を明示的に指定して同じことを実現できます。 たとえば、エンティティ型が定義されている場合のようなので。
```Csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```
列挙型の値は、OnModelCreating でさらに構成を行わなくても、データベース内の文字列として保存されます。

## <a name="limitations"></a>制限事項

これには、値変換システムのいくつかの既知の現在制限があります。
* 前述のよう`null`変換することはできません。
* 現在、1 つのプロパティの複数の列またはその逆への変換を分散する方法はありません。
* 値の変換の使用は、式を SQL に変換する EF Core の機能に影響可能性があります。 このようなケースに対して警告が記録されます。
今後のリリースでは、これらの制限の削除を検討しています。
