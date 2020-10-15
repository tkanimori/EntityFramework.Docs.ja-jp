---
title: 関数のマッピング-SQLite データベースプロバイダー-EF Core
description: SQLite EF Core データベースプロバイダーの関数マッピング
author: bricelam
ms.date: 10/06/2020
uid: core/providers/sqlite/functions
ms.openlocfilehash: 0787981a0c6fa401a7ef4b4c3c2b406f78117ad1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066551"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>SQLite EF Core プロバイダーの関数マッピング

このページでは、SQLite プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。

## <a name="binary-functions"></a>Binary 関数

.NET                        | SQL                             | 追加されたバージョン:
--------------------------- | ------------------------------- | --------
メモリ.Contains (値)       | instr ( @bytes , char ( @value )) > 0 | EF Core 5.0
メモリ.数                | 長さ ( @bytes )                  | EF Core 5.0
メモリ.SequenceEqual (second) | @bytes = @second                | EF Core 5.0

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

.NET                  | SQL                                  | 追加されたバージョン:
--------------------- | ------------------------------------ | --------
-decimalValue         | ef_negate ( @decimalValue )             | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue 、ef_negate ( @d )) | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue 、 @d )       | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue 、 @d )         | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue 、 @d )            | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue 、 @d )            | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0    | EF Core 5.0
decimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0   | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0    | EF Core 5.0
decimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0   | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue 、 @d )             | EF Core 5.0
floatValue% d        | ef_mod ( @floatValue 、 @d )              | EF Core 5.0
Math. Abs (値)       | abs ( @value )
Math. Max (val1, val2)  | max ( @val1 , @val2 )
Math. Min (val1, val2)  | min ( @val1 , @val2 )
Math. Round (d)         | round ( @d )
Math. Round (d, digits) | round ( @d , @digits )

> [!TIP]
> *Ef*で始まる SQL 関数は、EF Core によって作成されます。

## <a name="string-functions"></a>文字列関数

.NET                                                         | SQL                                                    | 追加されたバージョン:
------------------------------------------------------------ | ------------------------------------------------------ | --------
EF.関数。 Collate (オペランド、照合順序)                     | @operand 照合 @collation                            | EF Core 5.0
EF.関数。 Like (matchExpression、pattern)                  | @matchExpression という感じで @pattern
EF.関数。 Like (matchExpression、pattern、escapeCharacter) | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
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
