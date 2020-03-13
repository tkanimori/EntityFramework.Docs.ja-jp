---
title: EF Core 5.0 の新機能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 65d7bd43e8a00c77fd6091a74c677635710d03e3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413847"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 の新機能

EF Core 5.0 は現在開発中です。
このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。
EF Core 5.0 の最初のプレビューは、現在のところ 2020 年の第一四半期になると予想されています。

このページには [EF Core 5.0 のプラン](plan.md)を記載していません。
このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。

公開されている公式ドキュメントについては、リンクが追加されます。

## <a name="preview-1-not-yet-shipped"></a>プレビュー 1 (未出荷)

### <a name="simple-logging"></a>シンプルなログ

この機能により、EF6 の `Database.Log` に似た機能が追加されます。
つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。

暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。

追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。

### <a name="simple-way-to-get-generated-sql"></a>生成された SQL の取得方法がシンプルに

EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。

暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。

追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。

### <a name="enhanced-debug-views"></a>強化されたデバッグ ビュー

デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。
モデルのデバッグ ビューは少し前に実装されました。
EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。

暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。

追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>初期化された DbContext で接続または接続文字列が変更可能に

接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。
また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。
これにより、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。

ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。

### <a name="change-tracking-proxies"></a>変更追跡のプロキシ

EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。
これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。
ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません

ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。

### <a name="improved-handling-of-database-null-semantics"></a>データベースの null セマンティクスの処理の向上

リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。
一方、C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。
EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。
EF Core 5.0 では、この変換の効率が大幅に向上します。

ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。

### <a name="indexer-properties"></a>インデクサーのプロパティ

EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。
これにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。

ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列挙型マッピングの CHECK 制約の生成

EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。
次に例を示します。

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。

### <a name="query-translations-for-more-datetime-constructs"></a>より多くの DateTime コンストラクトに対するクエリ変換

新しい DataTime のコンストラクトが含まれるクエリを変換できるようになりました。
また、SQL Server 関数 DateDiffWeek がマップされるようになりました。

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translations-for-more-byte-array-constructs"></a>より多くのバイト配列コンストラクトに対するクエリ変換

byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。

暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。

追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-reverse"></a>Reverse のクエリ変換

`Reverse` を使用したクエリが変換されるようになりました。
次に例を示します。

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-bitwise-operators"></a>ビット処理演算子のクエリ変換

ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos の文字列のクエリ変換

文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。
