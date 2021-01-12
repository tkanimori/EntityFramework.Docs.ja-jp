---
title: .NET イベント-EF Core
description: EF Core によって定義された .NET イベント
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 0888009af0bedfb63690e72c4a0e08979a9e9cf3
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129253"
---
# <a name="net-events-in-ef-core"></a>EF Core での .NET イベント

> [!TIP]
> GitHub から [イベントのサンプルをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) できます。

Entity Framework Core (EF Core) は、EF Core コードで特定の処理が発生したときにコールバックとして機能する [.net イベント](/dotnet/standard/events/) を公開します。 イベントは [インターセプター](xref:core/logging-events-diagnostics/interceptors) よりも単純であり、より柔軟な登録が可能です。 ただし、これらは同期のみであるため、非ブロッキングの非同期 I/O を実行することはできません。

イベントはインスタンスごとに登録され `DbContext` ます。 プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。

## <a name="events-raised-by-ef-core"></a>EF Core によって発生したイベント

EF Core によって、次のイベントが発生します。

| event | 導入されたバージョン | 発生した場合
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | 5.0 | またはの開始時 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | 5.0 | 成功した場合、 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> または <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | 5.0 | 失敗したまたはの終了時 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | エンティティがコンテキストによって追跡される場合
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | 追跡対象のエンティティの状態が変化したとき

### <a name="example-timestamp-state-changes"></a>例: タイムスタンプ状態の変更

DbContext によって追跡される各エンティティには、があり <xref:Microsoft.EntityFrameworkCore.EntityState> ます。 たとえば、状態は、 `Added` エンティティがデータベースに挿入されることを示します。

この例では、イベントとイベントを使用して、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> エンティティの状態が変化したことを検出します。 次に、この変更が発生した日時を示す現在の時刻を使用して、エンティティをスタンプします。 これにより、エンティティが挿入、削除、または最終更新された日時を示すタイムスタンプが生成されます。

この例のエンティティ型では、タイムスタンププロパティを定義するインターフェイスが実装されています。

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

アプリケーションの DbContext のメソッドは、このインターフェイスを実装する任意のエンティティに対してタイムスタンプを設定できます。

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

このメソッドには、イベントとイベントの両方のイベントハンドラーとして使用する適切なシグネチャがあり `Tracked` `StateChanged` ます。 このハンドラーは、DbContext コンストラクターの両方のイベントに登録されています。 イベントはいつでも DbContext にアタッチできることに注意してください。コンテキストコンストラクターでこれが行われる必要はありません。

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

2つのイベントが必要になるのは、新しいエンティティ `Tracked` が最初に追跡されたときにイベントを発生させるためです。 `StateChanged` イベントは、 _既に_ 追跡されている状態を変更するエンティティに対してのみ発生します。

この例の [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) には、ブログデータベースを変更する単純なコンソールアプリケーションが含まれています。

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

このコードからの出力は、発生した状態の変化と、適用されているタイムスタンプを示しています。

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
