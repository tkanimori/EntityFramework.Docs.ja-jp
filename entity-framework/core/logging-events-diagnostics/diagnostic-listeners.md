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
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="9efe3-103">EF Core での診断リスナーの使用</span><span class="sxs-lookup"><span data-stu-id="9efe3-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]  
> <span data-ttu-id="9efe3-104">[この記事のサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners)は GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="9efe3-105">診断リスナーを使用すると、現在の .NET プロセスで発生する任意の EF Core イベントをリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="9efe3-106">クラスは、 <xref:System.Diagnostics.DiagnosticListener> 実行中のアプリケーションから診断情報を取得するための、 [.net 全体の共通メカニズム](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) の一部です。</span><span class="sxs-lookup"><span data-stu-id="9efe3-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="9efe3-107">診断リスナーは、単一の DbContext インスタンスからイベントを取得するのに適していません。</span><span class="sxs-lookup"><span data-stu-id="9efe3-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="9efe3-108">EF Core [インターセプター](xref:core/logging-events-diagnostics/interceptors) は、コンテキストごとの登録で同じイベントにアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="9efe3-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="9efe3-109">診断リスナーは、ログ記録用には設計されていません。</span><span class="sxs-lookup"><span data-stu-id="9efe3-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="9efe3-110">ログ記録には、 [単純なログ](xref:core/logging-events-diagnostics/simple-logging) 記録または [Microsoft 拡張子のログ](xref:core/logging-events-diagnostics/extensions-logging) 記録を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="9efe3-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="9efe3-111">例: 診断イベントの監視</span><span class="sxs-lookup"><span data-stu-id="9efe3-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="9efe3-112">EF Core イベントの解決は、2段階のプロセスです。</span><span class="sxs-lookup"><span data-stu-id="9efe3-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="9efe3-113">まず、それ自体の [オブザーバー](/dotnet/standard/events/observer-design-pattern) を `DiagnosticListener` 作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9efe3-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

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

<span data-ttu-id="9efe3-114">`OnNext`メソッドは EF Core から取得した DiagnosticListener を検索します。</span><span class="sxs-lookup"><span data-stu-id="9efe3-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="9efe3-115">このリスナーの名前は "Microsoft. EntityFrameworkCore" で、次 <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> に示すようにクラスから取得できます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="9efe3-116">このオブザーバーは、アプリケーションのメソッドなど、グローバルに登録する必要があり `Main` ます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="9efe3-117">次に、EF Core DiagnosticListener が見つかると、新しいキー値オブザーバーが作成され、実際の EF Core イベントをサブスクライブします。</span><span class="sxs-lookup"><span data-stu-id="9efe3-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="9efe3-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9efe3-118">For example:</span></span>

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

<span data-ttu-id="9efe3-119">メソッドは、 `OnNext` EF Core イベントごとにキーと値のペアを指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="9efe3-120">キーはイベントの名前で、次のいずれかから取得できます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="9efe3-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> すべての EF Core データベースプロバイダーに共通のイベントの場合</span><span class="sxs-lookup"><span data-stu-id="9efe3-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="9efe3-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> すべてのリレーショナルデータベースプロバイダーに共通のイベントの場合</span><span class="sxs-lookup"><span data-stu-id="9efe3-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="9efe3-123">現在のデータベースプロバイダーに固有のイベントの類似クラス。</span><span class="sxs-lookup"><span data-stu-id="9efe3-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="9efe3-124">たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server プロバイダーの場合はです。</span><span class="sxs-lookup"><span data-stu-id="9efe3-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="9efe3-125">キー/値ペアの値は、イベントに固有のペイロードの種類です。</span><span class="sxs-lookup"><span data-stu-id="9efe3-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="9efe3-126">想定するペイロードの種類は、これらのイベントクラスで定義されている各イベントに記載されています。</span><span class="sxs-lookup"><span data-stu-id="9efe3-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="9efe3-127">たとえば、上記のコードでは、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> イベントとイベントを処理し <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> ます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="9efe3-128">最初の部分では、ペイロードは <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> です。</span><span class="sxs-lookup"><span data-stu-id="9efe3-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="9efe3-129">2つ目は、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> です。</span><span class="sxs-lookup"><span data-stu-id="9efe3-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="9efe3-130">ToString は、イベントの同等のログメッセージを生成するために、すべての EF Core イベントデータクラスでオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="9efe3-131">たとえば、を呼び出すと、 `ContextInitializedEventData.ToString` "Entity Framework Core 2.20475.6" というプロバイダー ' Microsoft. EntityFrameworkCore. Sqlite ' を使用して ' ブログ Scontext ' が初期化され、オプション: None "が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9efe3-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0-rc.2.20475.6 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="9efe3-132">この [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) には、ブログデータベースを変更し、検出された診断イベントを出力する単純なコンソールアプリケーションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9efe3-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

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

<span data-ttu-id="9efe3-133">このコードからの出力は、検出されたイベントを示しています。</span><span class="sxs-lookup"><span data-stu-id="9efe3-133">The output from this code shows the events detected:</span></span>

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
