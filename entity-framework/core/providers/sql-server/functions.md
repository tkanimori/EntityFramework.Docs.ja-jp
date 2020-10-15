---
title: 関数のマッピング-Microsoft SQL Server データベースプロバイダー-EF Core
description: Microsoft SQL Server データベースプロバイダーの関数マッピング
author: bricelam
ms.date: 10/07/2020
uid: core/providers/sql-server/functions
ms.openlocfilehash: 8eb66521b00f2f4879a098200239849c7219a095
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066563"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Microsoft SQL Server プロバイダーの関数マッピング

このページでは、SQL Server プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。

## <a name="binary-functions"></a>Binary 関数

.NET                         | SQL                           | 追加されたバージョン:
---------------------------- | ----------------------------- | --------
メモリ.Contains (値)        | CHARINDEX ( @value , @bytes ) > 0 | EF Core 5.0
メモリ.数                 | DATALENGTH ( @bytes )            | EF Core 5.0
メモリ.SequenceEqual (second)  | @bytes = @second              | EF Core 5.0
EF.関数. DataLength (arg) | DATALENGTH ( @arg )              | EF Core 5.0

## <a name="conversion-functions"></a>変換関数

.NET                      | SQL                                    | 追加されたバージョン:
------------------------- | -------------------------------------- | --------
メモリ.ToString ()          | CONVERT (varchar (100), @bytes )
byteValue。 ToString ()      | CONVERT (varchar (3), @byteValue )
charValue。 ToString ()      | CONVERT (varchar (1), @charValue )
Convert. ToBoolean (値)  | CONVERT (bit, @value )                   | EF Core 5.0
Convert. ToByte (値)     | CONVERT (tinyint, @value )
Convert. ToDecimal (値)  | CONVERT (decimal (18, 2), @value )
Convert. ToDouble (値)   | CONVERT (float, @value )
ToInt16 (値)    | CONVERT (smallint, @value )
ToInt32 (値)    | CONVERT (int, @value )
ToInt64 (値)    | CONVERT (bigint, @value )
Convert. ToString (値)   | CONVERT (nvarchar (max), @value )
dateTime. ToString ()       | CONVERT (varchar (100), @dateTime )
dateTimeOffset () | CONVERT (varchar (100), @dateTimeOffset )
decimalValue ()   | CONVERT (varchar (100), @decimalValue )
doubleValue ()    | CONVERT (varchar (100), @doubleValue )
floatValue ()     | CONVERT (varchar (100), @floatValue )
guid.ToString ()           | CONVERT (varchar (36), @guid )
intValue。 ToString ()       | CONVERT (varchar (11), @intValue )
longValue。 ToString ()      | CONVERT (varchar (20), @longValue )
sbyteValue. ToString ()     | CONVERT (varchar (4), @sbyteValue )
短い値です。 ToString ()     | CONVERT (varchar (6), @shortValue )
timeSpan。 ToString ()       | CONVERT (varchar (100), @timeSpan )
uintValue。 ToString ()      | CONVERT (varchar (10), @uintValue )
ulongValue ()     | CONVERT (varchar (19), @ulongValue )
ushortValue ()    | CONVERT (varchar (5), @ushortValue )

## <a name="date-and-time-functions"></a>日付と時刻関数

.NET                                                        | SQL                                                  | 追加されたバージョン:
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE ()
DateTime. 今日                                              | CONVERT (date, GETDATE ())
DateTime.UtcNow                                             | GETUTCDATE ()
AddDays (値)                                     | DATEADD (day, @value , @dateTime )
dateTime. AddHours (値)                                    | DATEADD (hour, @value , @dateTime )
dateTime. AddMilliseconds (値)                             | DATEADD (ミリ秒、 @value 、 @dateTime )
dateTime. AddMinutes (値)                                  | DATEADD (minute, @value , @dateTime )
dateTime. AddMonths (months)                                  | DATEADD (month, @months , @dateTime )
dateTime. AddSeconds (値)                                  | DATEADD (second, @value , @dateTime )
dateTime. AddYears (値)                                    | DATEADD (year, @value , @dateTime )
dateTime. Date                                               | CONVERT (date, @dateTime )
dateTime. Day                                                | DATEPART (day, @dateTime )
DayOfYear                                          | DATEPART (dayofyear, @dateTime )
dateTime. Hour                                               | DATEPART (hour, @dateTime )
dateTime. ミリ秒                                        | DATEPART (ミリ秒、 @dateTime )
dateTime. Minute                                             | DATEPART (分, @dateTime )
dateTime. 月                                              | DATEPART (month, @dateTime )
dateTime. Second                                             | DATEPART (second, @dateTime )
dateTime. TimeOfDay                                          | CONVERT (time, @dateTime )                              | EF Core 2.2
dateTime. Year                                               | DATEPART (year, @dateTime )
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET ()
UtcNow                                       | SYSUTCDATETIME ()
AddDays (日)                                | DATEADD (day, @days , @dateTimeOffset )
dateTimeOffset. AddHours (時間)                              | DATEADD (hour, @hours , @dateTimeOffset )
dateTimeOffset. AddMilliseconds (ミリ秒)                | DATEADD (ミリ秒、 @milliseconds 、 @dateTimeOffset )
dateTimeOffset. AddMinutes (minutes)                          | DATEADD (minute, @minutes , @dateTimeOffset )
dateTimeOffset. AddMonths (months)                            | DATEADD (month, @months , @dateTimeOffset )
dateTimeOffset. AddSeconds (秒)                          | DATEADD (second, @seconds , @dateTimeOffset )
dateTimeOffset. AddYears (年)                              | DATEADD (year, @years , @dateTimeOffset )
dateTimeOffset. 日付                                         | CONVERT (date, @dateTimeOffset )
dateTimeOffset. Day                                          | DATEPART (day, @dateTimeOffset )
DayOfYear                                    | DATEPART (dayofyear, @dateTimeOffset )
dateTimeOffset. Hour                                         | DATEPART (hour, @dateTimeOffset )
dateTimeOffset. ミリ秒                                  | DATEPART (ミリ秒、 @dateTimeOffset )
dateTimeOffset. Minute                                       | DATEPART (分, @dateTimeOffset )
dateTimeOffset. 月                                        | DATEPART (month, @dateTimeOffset )
dateTimeOffset. Second                                       | DATEPART (second, @dateTimeOffset )
dateTimeOffset. TimeOfDay                                    | CONVERT (time, @dateTimeOffset )                        | EF Core 2.2
dateTimeOffset. 年                                         | DATEPART (year, @dateTimeOffset )
EF.DateDiffDay (start, end)                        | DATEDIFF (day, @start , @end )
EF.DateDiffHour (start, end)                       | DATEDIFF (hour, @start , @end )
EF.DateDiffMicrosecond (start, end)                | DATEDIFF (マイクロ秒、 @start 、 @end )
EF.DateDiffMillisecond (start, end)                | DATEDIFF (ミリ秒、 @start 、 @end )
EF.DateDiffMinute (start, end)                     | DATEDIFF (minute, @start , @d2 )
EF.DateDiffMonth (start, end)                      | DATEDIFF (month, @start , @end )
EF.DateDiffNanosecond (start, end)                 | DATEDIFF (ナノ秒、 @start 、 @end )
EF.DateDiffSecond (start, end)                     | DATEDIFF (second, @start , @end )
EF.DateDiffWeek (start, end)                       | DATEDIFF (week, @start , @end )                         | EF Core 5.0
EF.DateDiffYear (start, end)                       | DATEDIFF (year, @start , @end )
EF.関数. DateFromParts (年、月、日)                | DATEFROMPARTS ( @year 、 @month 、 @day )                   | EF Core 5.0
EF.DateTime2FromParts (year、month、day、...)      | DATETIME2FROMPARTS ( @year , @month , @day ,...)         | EF Core 5.0
EF.DateTimeFromParts (year、month、day、...)       | DATETIMEFROMPARTS ( @year , @month , @day ,...)          | EF Core 5.0
EF.DateTimeOffsetFromParts (year、month、day、...) | DATETIMEOFFSETFROMPARTS ( @year , @month , @day ,...)    | EF Core 5.0
EF.関数. IsDate (式)                             | ISDATE ( @expression )                                  | EF Core 3.0
EF.SmallDateTimeFromParts (year、month、day、...)  | SMALLDATETIMEFROMPARTS ( @year , @month , @day ,...)     | EF Core 5.0
EF.関数。 TimeFromParts (hour, minute, second,...)       | TIMEFROMPARTS ( @hour 、 @minute 、 @second 、...)          | EF Core 5.0
timeSpan. Hours                                              | DATEPART (hour, @timeSpan )                            | EF Core 5.0
timeSpan. ミリ秒                                       | DATEPART (ミリ秒、 @timeSpan )                     | EF Core 5.0
timeSpan. 分                                            | DATEPART (分, @timeSpan )                          | EF Core 5.0
timeSpan. 秒                                            | DATEPART (second, @timeSpan )                          | EF Core 5.0

## <a name="numeric-functions"></a>数値関数

.NET                    | SQL
----------------------- | ---
Math. Abs (値)         | ABS ( @value )
Math. Acos (d)            | ACOS ( @d )
数値演算 (d)            | アークサイン ( @d )
Atan (d)            | ATAN ( @d )
Math. Atan2 (y, x)        | ATN2 ( @y , @x )
数学. 天井 (d)         | シーリング ( @d )
数値演算 Cos (d)             | COS ( @d )
Math. Exp (d)             | EXP ( @d )
数値演算フロア (d)           | FLOOR ( @d )
数値演算ログ (d)             | LOG ( @d )
Math .Log (a、newBase)    | LOG ( @a , @newBase )
Log10 (d)           | LOG10 ( @d )
Math. Pow (x, y)          | POWER ( @x , @y )
Math. Round (d)           | ROUND ( @d , 0)
Math. Round (d, decimal) | ROUND ( @d , @decimals )
Math. Sign (値)        | SIGN ( @value )
Math. Sin (a)             | SIN ( @a )
Math. Sqrt (d)            | SQRT ( @d )
Math. Tan (a)             | TAN ( @a )
Math. Truncate (d)        | ROUND ( @d , 0, 1)

## <a name="string-functions"></a>文字列関数

.NET                                                                    | SQL                                                                    | 追加されたバージョン:
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
EF.関数。 Collate (オペランド、照合順序)                                | @operand 照合 @collation                                            | EF Core 5.0
EF.Functions。 Contains (propertyReference, searchCondition)               | CONTAINS ( @propertyReference , @searchCondition )                         | EF Core 2.2
EF.Functions。 Contains (propertyReference, searchCondition, 言語用語) | ( @propertyReference 、 @searchCondition 、言語 @languageTerm ) を含みます。 | EF Core 2.2
EF.関数 FreeText (propertyReference, freeText)                      | FREETEXT ( @propertyReference 、 @freeText )
EF.関数 FreeText (propertyReference, freeText, 言語用語)        | FREETEXT ( @propertyReference 、 @freeText 、言語 @languageTerm )
EF.関数。 Like (matchExpression、pattern)                             | @matchExpression という感じで @pattern
EF.関数。 Like (matchExpression、pattern、escapeCharacter)            | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
String.compare (strA, strB)                                              | 場合: @strA = @strB THEN 0...終わり
文字列.Concat (str0, str1)                                               | @str0 + @str1
文字列.IsNullOrEmpty (値)                                             | @value NULL または @value = N ' '
文字列.IsNullOrWhiteSpace (値)                                        | @value が NULL または LTRIM (RTRIM ( @value )) = N ' ' です。
stringValue (strB)                                             | 場合: @stringValue = @strB THEN 0...終わり
stringValue。 Contains (値)                                             | @stringValue LIKE N '% ' + @value + N '% '
EndsWith (値)                                             | @stringValue LIKE N '% ' + @value
FirstOrDefault ()                                            | SUBSTRING ( @stringValue , 1, 1)                                          | EF Core 5.0
stringValue (値)                                              | CHARINDEX ( @value , @stringValue )-1
LastOrDefault ()                                             | SUBSTRING ( @stringValue , LEN ( @stringValue ), 1)                          | EF Core 5.0
文字列の長さ                                                      | LEN ( @stringValue )
stringValue ( @oldValue , @newValue )                               | 置換 ( @stringValue , @oldValue , @newValue )
StartsWith (値)                                           | @stringValue LIKE @value + N '% '
stringValue (startIndex, length)                               | SUBSTRING ( @stringValue , @startIndex + 1, @length )
ToLower ()                                                   | LOWER ( @stringValue )
ToUpper ()                                                   | UPPER ( @stringValue )
stringValue ()                                                      | LTRIM (RTRIM ( @stringValue ))
TrimEnd ()                                                   | RTRIM ( @stringValue )
TrimStart ()                                                 | LTRIM ( @stringValue )

## <a name="miscellaneous-functions"></a>その他の関数

.NET                                     | SQL                                | 追加されたバージョン:
---------------------------------------- | ---------------------------------- | --------
表す.Contains (item)                | @item から @collection               | EF Core 3.0
enumValue. HasFlag (フラグ)                  | @enumValue & @flag = @flag
Guid.NewGuid()                           | NEWID()
nullable.GetValueOrDefault ()             | 合体 ( @nullable , 0)
nullable.GetValueOrDefault (defaultValue) | 合体 ( @nullable , @defaultValue )

> [!NOTE]
> いくつかの SQL は、例示のために簡略化されています。 実際の SQL は、より広い範囲の値を処理するよりも複雑です。

## <a name="see-also"></a>関連項目

* [空間関数のマッピング](xref:core/providers/sql-server/spatial#spatial-function-mappings)
