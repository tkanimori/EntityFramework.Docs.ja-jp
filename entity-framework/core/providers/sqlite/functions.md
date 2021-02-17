---
title: 関数のマッピング-SQLite データベースプロバイダー-EF Core
description: SQLite EF Core データベースプロバイダーの関数マッピング
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sqlite/functions
ms.openlocfilehash: 66fb24219465b8c34407d279450b7d501e7a572d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543212"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>SQLite EF Core プロバイダーの関数マッピング

このページでは、SQLite プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。

## <a name="binary-functions"></a>Binary 関数

.NET                                           | SQL                                  | 追加されたバージョン:
---------------------------------------------- | ------------------------------------ | --------
メモリ.Contains (値)                          | instr ( @bytes , char ( @value )) > 0      | EF Core 5.0
メモリ.数                                   | 長さ ( @bytes )                       | EF Core 5.0
メモリ.SequenceEqual (second)                    | @bytes = @second                     | EF Core 5.0
EF.関数. Hex (バイト)                        | hex ( @bytes )                          | EF Core 6.0
EF.Substr (bytes, startIndex)         | substr ( @bytes , @startIndex )          | EF Core 6.0
EF.Substr (bytes, startIndex, length) | substr ( @bytes , @startIndex , @length ) | EF Core 6.0

## <a name="conversion-functions"></a>変換関数

.NET                      | SQL                           | 追加されたバージョン:
------------------------- | ----------------------------- | --------
ブール値。 ToString ()      | CAST ( @boolValue テキストとして)      | EF Core 6.0
byteValue。 ToString ()      | CAST ( @byteValue テキストとして)      | EF Core 6.0
メモリ.ToString ()          | CAST ( @bytes テキストとして)          | EF Core 6.0
charValue。 ToString ()      | CAST ( @charValue テキストとして)      | EF Core 6.0
dateTime. ToString ()       | CAST ( @dateTime テキストとして)       | EF Core 6.0
dateTimeOffset () | CAST ( @dateTimeOffset テキストとして) | EF Core 6.0
decimalValue ()   | CAST ( @decimalValue テキストとして)   | EF Core 6.0
doubleValue ()    | CAST ( @doubleValue テキストとして)    | EF Core 6.0
floatValue ()     | CAST ( @floatValue テキストとして)     | EF Core 6.0
guid.ToString ()           | CAST ( @guid テキストとして)           | EF Core 6.0
intValue。 ToString ()       | CAST ( @intValue テキストとして)       | EF Core 6.0
longValue。 ToString ()      | CAST ( @longValue テキストとして)      | EF Core 6.0
sbyteValue. ToString ()     | CAST ( @sbyteValue テキストとして)     | EF Core 6.0
短い値です。 ToString ()     | CAST ( @shortValue テキストとして)     | EF Core 6.0
timeSpan。 ToString ()       | CAST ( @timeSpan テキストとして)       | EF Core 6.0
uintValue。 ToString ()      | CAST ( @uintValue テキストとして)      | EF Core 6.0
ushortValue ()    | CAST ( @ushortValue テキストとして)    | EF Core 6.0

## <a name="date-and-time-functions"></a>日付と時刻関数

.NET                            | SQL                                                                      | 追加されたバージョン:
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | datetime (' now ', ' localtime ')
DateTime. 今日                  | datetime (' now ', ' localtime ', ' start of day ')
DateTime.UtcNow                 | datetime (' now ')
AddDays (値)         | datetime ( @dateTime , @value \| \| ' days ')
dateTime. AddHours (値)        | datetime ( @dateTime , @d \| \| ' hours ')
dateTime. AddMilliseconds (値) | datetime ( @dateTime , ( @value /1000.0) \| \| ' seconds ')                   | EF Core 2.2
dateTime. AddMinutes (値)      | datetime ( @dateTime , @value \| \| ' minutes ')
dateTime. AddMonths (months)      | datetime ( @dateTime , @months \| \| ' months ')
dateTime. AddSeconds (値)      | datetime ( @dateTime , @value \| \| ' seconds ')
dateTime. AddTicks (値)        | datetime ( @dateTime , ( @value /10000000.0) \| \| ' seconds ')               | EF Core 2.2
dateTime. AddYears (値)        | datetime ( @dateTime , @value \| \| ' years ')
dateTime. Date                   | datetime ( @dateTime , ' day of day ')
dateTime. Day                    | strftime ('% d ', @dateTime )
dateTime. DayOfWeek              | strftime ('% w ', @dateTime )                                                | EF Core 2.2
DayOfYear              | strftime ('% j ', @dateTime )
dateTime. Hour                   | strftime ('% H ', @dateTime )
dateTime. ミリ秒            | (strftime ('% f ', @dateTime ) * 1000) %1000
dateTime. Minute                 | strftime ('% M ', @dateTime )
dateTime. 月                  | strftime ('% m ', @dateTime )
dateTime. Second                 | strftime ('% S ', @dateTime )
dateTime. ティック                  | (julianday ( @dateTime )-julianday (' 0001-01-01 00:00:00 ')) * 8640億 | EF Core 2.2
dateTime. TimeOfDay              | 時刻 ( @dateTime )                                                          | EF Core 3.0
dateTime. Year                   | strftime ('% Y ', @dateTime )

> [!NOTE]
> いくつかの SQL は、例示のために簡略化されています。 実際の SQL は、より広い範囲の値を処理するよりも複雑です。

## <a name="numeric-functions"></a>数値関数

.NET                  | SQL                                   | 追加されたバージョン:
--------------------- | ------------------------------------- | --------
-decimalValue         | ef_negate ( @decimalValue )              | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue 、ef_negate ( @d ))  | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue 、 @d )        | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue 、 @d )          | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue 、 @d )             | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue 、 @d )             | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0     | EF Core 5.0
decimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0    | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0     | EF Core 5.0
decimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0    | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue 、 @d )              | EF Core 5.0
EF.Functions。 Random () | abs (random ()/9223372036854780000.0) | EF Core 6.0
floatValue% d        | ef_mod ( @floatValue 、 @d )               | EF Core 5.0
Math. Abs (値)       | abs ( @value )
Math. Max (val1, val2)  | max ( @val1 , @val2 )
Math. Min (val1, val2)  | min ( @val1 , @val2 )
Math. Round (d)         | round ( @d )
Math. Round (d, digits) | round ( @d , @digits )

> [!TIP]
> *Ef* で始まる SQL 関数は、EF Core によって作成されます。

## <a name="string-functions"></a>文字列関数

.NET                                                         | SQL                                                    | 追加されたバージョン:
------------------------------------------------------------ | ------------------------------------------------------ | --------
char.ToLower (c)                                              | lower ( @c )                                              | EF Core 6.0
char.ToUpper (c)                                              | upper ( @c )                                              | EF Core 6.0
EF.関数。 Collate (オペランド、照合順序)                     | @operand 照合 @collation                            | EF Core 5.0
EF.Functions. Glob (matchExpression, pattern)                  | glob ( @pattern , @matchExpression )                       | EF Core 6.0
EF.関数。 Like (matchExpression、pattern)                  | @matchExpression という感じで @pattern
EF.関数。 Like (matchExpression、pattern、escapeCharacter) | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
IsMatch (input, pattern)                                | regexp ( @pattern , @input )                               | EF Core 6.0
String.compare (strA, strB)                                   | 場合: @strA = @strB THEN 0...終わり
文字列.Concat (str0, str1)                                    | @str0 \|\| @str1
文字列.IsNullOrEmpty (値)                                  | @value IS NULL または @value = ' '
文字列.IsNullOrWhiteSpace (値)                             | @value IS NULL または trim ( @value ) = ' '
stringValue (strB)                                  | 場合: @stringValue = @strB THEN 0...終わり
stringValue。 Contains (値)                                  | instr ( @stringValue , @value ) > 0
EndsWith (値)                                  | @stringValueLIKE '% ' \| \|@value
FirstOrDefault ()                                 | substr ( @stringValue , 1, 1)                             | EF Core 5.0
stringValue (値)                                   | instr ( @stringValue , @value )-1
LastOrDefault ()                                  | substr ( @stringValue , length ( @stringValue ), 1)          | EF Core 5.0
文字列の長さ                                           | 長さ ( @stringValue )
stringValue (oldValue, newValue)                      | 置換 ( @stringValue , @oldValue , @newValue )
StartsWith (値)                                | @stringValueLIKE @value \| \| '% '
stringValue (startIndex, length)                    | substr ( @stringValue , @startIndex + 1, @length )
ToLower ()                                        | lower ( @stringValue )
ToUpper ()                                        | upper ( @stringValue )
stringValue ()                                           | trim ( @stringValue )
stringValue (trimChar)                                   | trim ( @stringValue , @trimChar )
TrimEnd ()                                        | rtrim ( @stringValue )
TrimEnd (trimChar)                                | rtrim ( @stringValue , @trimChar )
TrimStart ()                                      | ltrim ( @stringValue )
TrimStart (trimChar)                              | ltrim ( @stringValue , @trimChar )

> [!NOTE]
> いくつかの SQL は、例示のために簡略化されています。 実際の SQL は、より広い範囲の値を処理するよりも複雑です。

## <a name="miscellaneous-functions"></a>その他の関数

.NET                                     | SQL                                | 追加されたバージョン:
---------------------------------------- | ---------------------------------- | --------
表す.Contains (item)                | @item から @collection               | EF Core 3.0
enumValue. HasFlag (フラグ)                  | @enumValue & @flag = @flag
nullable.GetValueOrDefault ()             | 合体 ( @nullable , 0)
nullable.GetValueOrDefault (defaultValue) | 合体 ( @nullable , @defaultValue )

## <a name="see-also"></a>関連項目

* [空間関数のマッピング](xref:core/providers/sqlite/spatial#spatial-function-mappings)
