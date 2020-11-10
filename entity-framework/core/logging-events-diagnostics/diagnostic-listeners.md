---
title: 診断リスナーの使用-EF Core
description: EF Core 診断のグローバルな消費に DiagnosticListener を使用する
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: dba82a910e2b551e692f37d721d41968981849cf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431297"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>EF Core での診断リスナーの使用

> [!TIP]  
> [この記事のサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners)は GitHub からダウンロードできます。

診断リスナーを使用すると、現在の .NET プロセスで発生する任意の EF Core イベントをリッスンできます。 クラスは、 <xref:System.Diagnostics.DiagnosticListener> 実行中のアプリケーションから診断情報を取得するための、 [.net 全体の共通メカニズム](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) の一部です。

診断リスナーは、単一の DbContext インスタンスからイベントを取得するのに適していません。 EF Core [インターセプター](xref:core/logging-events-diagnostics/interceptors) は、コンテキストごとの登録で同じイベントにアクセスできるようにします。

診断リスナーは、ログ記録用には設計されていません。 ログ記録には、 [単純なログ](xref:core/logging-events-diagnostics/simple-logging) 記録または [Microsoft 拡張子のログ](xref:core/logging-events-diagnostics/extensions-logging) 記録を使用することを検討してください。

## <a name="example-observing-diagnostic-events"></a>例: 診断イベントの監視

EF Core イベントの解決は、2段階のプロセスです。 まず、それ自体の [オブザーバー](/dotnet/standard/events/observer-design-pattern) を `DiagnosticListener` 作成する必要があります。

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

`OnNext`メソッドは EF Core から取得した DiagnosticListener を検索します。 このリスナーの名前は "Microsoft. EntityFrameworkCore" で、次 <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> に示すようにクラスから取得できます。

このオブザーバーは、アプリケーションのメソッドなど、グローバルに登録する必要があり `Main` ます。

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

次に、EF Core DiagnosticListener が見つかると、新しいキー値オブザーバーが作成され、実際の EF Core イベントをサブスクライブします。 次に例を示します。

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

メソッドは、 `OnNext` EF Core イベントごとにキーと値のペアを指定して呼び出されます。 キーはイベントの名前で、次のいずれかから取得できます。

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> すべての EF Core データベースプロバイダーに共通のイベントの場合
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> すべてのリレーショナルデータベースプロバイダーに共通のイベントの場合
* 現在のデータベースプロバイダーに固有のイベントの類似クラス。 たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server プロバイダーの場合はです。

キー/値ペアの値は、イベントに固有のペイロードの種類です。 想定するペイロードの種類は、これらのイベントクラスで定義されている各イベントに記載されています。

たとえば、上記のコードでは、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> イベントとイベントを処理し <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> ます。 最初の部分では、ペイロードは <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> です。 2つ目は、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> です。

> [!TIP]
> ToString は、イベントの同等のログメッセージを生成するために、すべての EF Core イベントデータクラスでオーバーライドされます。 たとえば、を呼び出すと、 `ContextInitializedEventData.ToString` "Entity Framework Core 2.20475.6" というプロバイダー ' Microsoft. EntityFrameworkCore. Sqlite ' を使用して ' ブログ Scontext ' が初期化され、オプション: None "が生成されます。

この [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) には、ブログデータベースを変更し、検出された診断イベントを出力する単純なコンソールアプリケーションが含まれています。

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion
        
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

このコードからの出力は、検出されたイベントを示しています。

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
