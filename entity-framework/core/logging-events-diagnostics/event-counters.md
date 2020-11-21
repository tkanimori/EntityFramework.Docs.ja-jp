---
title: イベントカウンター-EF Core
description: .NET イベントカウンターを使用して EF Core パフォーマンスを追跡し、異常を診断する
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 46acfe82d8aeb7d16146bae0cc2cd4ff733e2831
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003689"
---
# <a name="event-counters"></a>イベントカウンター

> [!NOTE]
> この機能は、EF Core 5.0 で追加されました。

Entity Framework Core (EF Core) は、継続的な数値メトリックを公開します。これにより、プログラムの正常性を正確に示すことができます。 これらのメトリックは、次の目的で使用できます。

* アプリケーションの実行中に、データベースの一般的な負荷をリアルタイムで追跡する
* パフォーマンスの低下につながる可能性がある問題のあるコーディングプラクティスを公開する
* 異常なプログラムの動作を追跡して特定する

EF Core は、標準の .NET イベントカウンター機能を使用してメトリックをレポートします。カウンターの動作の概要については、 [このブログ投稿](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) をお読みになることをお勧めします。

## <a name="attach-to-a-process-using-dotnet-counters"></a>Dotnet を使用してプロセスにアタッチする

[Dotnet ツール](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)を使用すると、実行中のプロセスにアタッチして EF Core イベントカウンターを定期的に報告できます。これらのカウンターを使用できるようにするために、プログラムで特別な操作を行う必要はありません。

まず、ツールをインストール `dotnet-counters` `dotnet tool install --global dotnet-counters` します。

次に、EF Core アプリケーションを実行している .NET プロセスのプロセス ID (PID) を確認します。

### <a name="windows"></a>[Windows](#tab/windows)

1. タスクバーを右クリックし、[タスクマネージャー] を選択して、Windows タスクマネージャーを開きます。
2. ウィンドウの下部にある [詳細] オプションが選択されていることを確認します。
3. [プロセス] タブで列を右クリックし、[PID] 列が有効になっていることを確認します。
4. [プロセス] の一覧でアプリケーションを見つけ、PID 列からプロセス ID を取得します。

### <a name="linux-or-macos"></a>[Linux または macOS](#tab/fluent-api)

1. コマンドを使用して、 `ps` ユーザーが実行しているすべてのプロセスを一覧表示します。
2. [プロセス] の一覧でアプリケーションを見つけ、PID 列からプロセス ID を取得します。

***

.NET アプリケーション内では、プロセス ID をとして使用できます `Process.GetCurrentProcess().Id` 。これは、起動時に PID を出力する場合に便利です。

最後に、次のように起動し `dotnet-counters` ます。

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` 次に、実行中のプロセスにアタッチして、継続的なカウンターデータのレポートを開始します。

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a>カウンターとその意味

カウンター名                          | 説明
------------------------------------- | ----
アクティブな DbContexts                     | 現在アプリケーション内にある、アクティブな破棄解除済みの DbContext インスタンスの数。 この数が継続的に増加している場合は、DbContext インスタンスが正しく破棄されていないためにリークが発生する可能性があります。 [コンテキストプーリング](xref:core/miscellaneous/context-pooling)が有効になっている場合、この数には、現在使用されていないプール済み dbcontext インスタンスが含まれていることに注意してください。
実行戦略操作の失敗 | データベース操作の実行に失敗した回数。 再試行中の実行戦略が有効になっている場合は、同じ操作に対して複数回試行されるたびに個々のエラーが発生します。 これは、インフラストラクチャに関する一時的な問題を検出するために使用できます。
オプティミスティック同時実行制御エラー       | `SaveChanges`オプティミスティック同時実行制御エラーのために失敗した回数。データストア内のデータは、コードの読み込み後に変更されています。 これは、 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> スローされるに対応します。
クエリ                               | 実行されたクエリの数。
クエリキャッシュヒット率 (%)              | クエリキャッシュヒットが失敗した割合。 指定された LINQ クエリが EF Core (パラメーターを除く) によって初めて実行された場合は、比較的負荷の大きいプロセスでコンパイルする必要があります。 通常のアプリケーションでは、すべてのクエリが再利用されます。また、クエリキャッシュヒット率は、初期ウォームアップ期間後の100% で安定している必要があります。 この数値が時間の経過と共に100% 未満の場合は、コンパイルが繰り返されるため、パフォーマンスが低下する可能性があります。これは、動的なクエリ生成が最適ではない可能性があります。
SaveChanges                           | `SaveChanges`が呼び出された回数。 では `SaveChanges` 複数の変更が1つのバッチに保存されるため、これは必ずしも1つのエンティティに対して行われた個々の更新を表すとは限りません。

## <a name="additional-resources"></a>その他のリソース

* [イベントカウンターに関する .NET ドキュメント](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
