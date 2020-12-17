---
title: .NET イベント-EF Core
description: EF Core によって定義された .NET イベント
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 51c0bba5cf25e1d9ddd1fd9aebea50b9a03481a3
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635693"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="a8614-103">EF Core での .NET イベント</span><span class="sxs-lookup"><span data-stu-id="a8614-103">.NET Events in EF Core</span></span>

> [!TIP]  
> <span data-ttu-id="a8614-104">GitHub から [イベントのサンプルをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) できます。</span><span class="sxs-lookup"><span data-stu-id="a8614-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="a8614-105">Entity Framework Core (EF Core) は、EF Core コードで特定の処理が発生したときにコールバックとして機能する [.net イベント](/dotnet/standard/events/) を公開します。</span><span class="sxs-lookup"><span data-stu-id="a8614-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="a8614-106">イベントは [インターセプター](xref:core/logging-events-diagnostics/interceptors) よりも単純であり、より柔軟な登録が可能です。</span><span class="sxs-lookup"><span data-stu-id="a8614-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="a8614-107">ただし、これらは同期のみであるため、非ブロッキングの非同期 I/O を実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="a8614-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="a8614-108">イベントはインスタンスごとに登録され `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="a8614-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="a8614-109">プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a8614-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="a8614-110">EF Core によって発生したイベント</span><span class="sxs-lookup"><span data-stu-id="a8614-110">Events raised by EF Core</span></span>

<span data-ttu-id="a8614-111">EF Core によって、次のイベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="a8614-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="a8614-112">Event</span><span class="sxs-lookup"><span data-stu-id="a8614-112">Event</span></span> | <span data-ttu-id="a8614-113">導入されたバージョン</span><span class="sxs-lookup"><span data-stu-id="a8614-113">Version introduced</span></span> | <span data-ttu-id="a8614-114">発生した場合</span><span class="sxs-lookup"><span data-stu-id="a8614-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="a8614-115">5.0</span><span class="sxs-lookup"><span data-stu-id="a8614-115">5.0</span></span> | <span data-ttu-id="a8614-116">またはの開始時 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="a8614-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="a8614-117">5.0</span><span class="sxs-lookup"><span data-stu-id="a8614-117">5.0</span></span> | <span data-ttu-id="a8614-118">成功した場合、 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> または <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="a8614-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="a8614-119">5.0</span><span class="sxs-lookup"><span data-stu-id="a8614-119">5.0</span></span> | <span data-ttu-id="a8614-120">失敗したまたはの終了時 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="a8614-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="a8614-121">2.1</span><span class="sxs-lookup"><span data-stu-id="a8614-121">2.1</span></span> | <span data-ttu-id="a8614-122">エンティティがコンテキストによって追跡される場合</span><span class="sxs-lookup"><span data-stu-id="a8614-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="a8614-123">2.1</span><span class="sxs-lookup"><span data-stu-id="a8614-123">2.1</span></span> | <span data-ttu-id="a8614-124">追跡対象のエンティティの状態が変化したとき</span><span class="sxs-lookup"><span data-stu-id="a8614-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="a8614-125">例: タイムスタンプ状態の変更</span><span class="sxs-lookup"><span data-stu-id="a8614-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="a8614-126">DbContext によって追跡される各エンティティには、があり <xref:Microsoft.EntityFrameworkCore.EntityState> ます。</span><span class="sxs-lookup"><span data-stu-id="a8614-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="a8614-127">たとえば、状態は、 `Added` エンティティがデータベースに挿入されることを示します。</span><span class="sxs-lookup"><span data-stu-id="a8614-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="a8614-128">この例では、イベントとイベントを使用して、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> エンティティの状態が変化したことを検出します。</span><span class="sxs-lookup"><span data-stu-id="a8614-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="a8614-129">次に、この変更が発生した日時を示す現在の時刻を使用して、エンティティをスタンプします。</span><span class="sxs-lookup"><span data-stu-id="a8614-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="a8614-130">これにより、エンティティが挿入、削除、または最終更新された日時を示すタイムスタンプが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a8614-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="a8614-131">この例のエンティティ型では、タイムスタンププロパティを定義するインターフェイスが実装されています。</span><span class="sxs-lookup"><span data-stu-id="a8614-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="a8614-132">アプリケーションの DbContext のメソッドは、このインターフェイスを実装する任意のエンティティに対してタイムスタンプを設定できます。</span><span class="sxs-lookup"><span data-stu-id="a8614-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

<span data-ttu-id="a8614-133">このメソッドには、イベントとイベントの両方のイベントハンドラーとして使用する適切なシグネチャがあり `Tracked` `StateChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="a8614-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="a8614-134">このハンドラーは、DbContext コンストラクターの両方のイベントに登録されています。</span><span class="sxs-lookup"><span data-stu-id="a8614-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="a8614-135">イベントはいつでも DbContext にアタッチできることに注意してください。コンテキストコンストラクターでこれが行われる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a8614-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="a8614-136">2つのイベントが必要になるのは、新しいエンティティ `Tracked` が最初に追跡されたときにイベントを発生させるためです。</span><span class="sxs-lookup"><span data-stu-id="a8614-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="a8614-137">`StateChanged` イベントは、 _既に_ 追跡されている状態を変更するエンティティに対してのみ発生します。</span><span class="sxs-lookup"><span data-stu-id="a8614-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="a8614-138">この例の [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) には、ブログデータベースを変更する単純なコンソールアプリケーションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a8614-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

<span data-ttu-id="a8614-139">このコードからの出力は、発生した状態の変化と、適用されているタイムスタンプを示しています。</span><span class="sxs-lookup"><span data-stu-id="a8614-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
