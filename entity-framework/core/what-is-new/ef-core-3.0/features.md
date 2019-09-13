---
title: EF Core 3.0 の新機能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: d61fa884f4669daa220ffc96ae59dd63518e6d5a
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921674"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 (現在、プレビュー段階) に含まれる新機能

> [!IMPORTANT]
> 機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。

以下のリストには、EF Core 3.0 について計画されている主な新機能が含まれています。
これらの機能のほとんどは現在のプレビューに含まれていませんが、RTM に向けて進行中であるため、使用できるようになります。

理由は、リリースの開始時に、計画されている[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)の実装に焦点を合わせているためです。
これらの破壊的変更の多くは、EF Core 自体の機能強化です。
さらに強化するために他にも多くの変更が必要です。 

進行中のバグ修正と機能強化に関する完全なリストについては、[問題の追跡ツールのこのクエリ](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)で確認できます。

## <a name="linq-improvements"></a>LINQ の機能強化 

[問題 #12795 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。

LINQ を使うと、好みの言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを活用できます。
ただし、LINQ では複雑なクエリを無制限に記述することもできます。これは、LINQ プロバイダーにとって常に重要な課題となります。
EF Core の最初のいくつかのバージョンでは、クエリのうち SQL に変換可能な部分を特定してから、クエリの残りの部分をクライアント側のメモリ内で実行させることにより、これを部分的に解決しました。
状況によってはこのクライアント側の実行が望ましいものとなる場合がありますが、その他の多くの場合では、アプリケーションが運用環境に展開されるまで特定できない非効率なクエリが発生する可能性があります。
EF Core 3.0 では、LINQ の実装のしくみとそのテスト方法に関して、大きな変更を加える予定です。
その目的は、これをより堅牢にすること (たとえば、修正プログラムのリリースで破壊的なクエリを回避すること)、より多くの式を適切に SQL に変換できるようにすること、効率的なクエリをより多くのケースで生成すること、および非効率なクエリの見逃しを防ぐことです。

## <a name="cosmos-db-support"></a>Cosmos DB のサポート 

[問題 #8443 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

この機能は現在のプレビューに含まれていますが、まだ完成していません。 

EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにするために、EF Core 用の Cosmos DB プロバイダーに取り組んでいます。
その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。
プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。
この作業は EF Core 2.2 以前に開始されたため、[いくつかのプロバイダーのプレビュー バージョンを利用できます](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。
新しいプランでは、EF Core 3.0 と共にプロバイダーの開発を継続していきます。 

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>プリンシパルとテーブルを共有する依存エンティティが省略可能になりました

[問題 #9005 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

この機能は、EF Core 3.0 プレビュー 4 で導入されます。

次のモデルがあるとします。
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

EF Core 3.0 以降では、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` なしで `Order` を追加することができるようになり、主キー以外のすべての `OrderDetails` プロパティは NULL 値が許可される列にマップされます。

クエリ時には、必須プロパティのいずれかに値がない場合、または主キー以外に必須プロパティがなく、すべてのプロパティが `null` である場合、EF Core によって `OrderDetails` が `null` に設定されます。

## <a name="c-80-support"></a>C# 8.0 のサポート

[問題 #12047 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[問題 #10347 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10347)

この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。

非同期ストリーム (`await foreach` を含む) や Null 許容参照型など、[C# 8.0 で導入される新機能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)の一部を EF Core を使う際にも利用していただきたいと考えています。

## <a name="reverse-engineering-of-database-views"></a>データベース ビューのリバース エンジニアリング

[問題 #1679 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

この機能は現在のプレビューに含まれていません。

EF Core 2.1 で導入され、EF Core 3.0 ではキーなしのエンティティ型が考慮された[クエリ型](xref:core/modeling/query-types)は、データベースから読み取れるものの、更新することはできないデータを表します。
この特性によって、ほとんどのシナリオのデータベース ビューに最適なものとなるため、データベース ビューのリバース エンジニアリング時にキーなしのエンティティ型の作成を自動化することを計画しています。

## <a name="ef-63-on-net-core"></a>.NET Core での EF 6.3

[問題 EF6#271 の追跡](https://github.com/aspnet/EntityFramework6/issues/271)

この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。 

多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると多大な労力が必要となる場合があります。
そのため、次のバージョンの EF 6 は .NET Core 3.0 上で実行するように調整されます。
最小限の変更で既存のアプリケーションを移植できるようにするために、この作業を行っています。
これにはいくつかの制限があります。 次に例を示します。
- .NET Core での SQL Server の含まれているサポート以外の、他のデータベースを操作するための新しいプロバイダーが必要になります
- SQL Server での空間サポートは有効になりません

この時点では、EF 6 について計画されている新機能がないことにも注意してください。
