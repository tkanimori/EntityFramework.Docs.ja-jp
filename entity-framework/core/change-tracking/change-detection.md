---
title: 変更の検出と通知-EF Core
description: 検出された変更または通知を使用したプロパティとリレーションシップの変更の検出
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129612"
---
# <a name="change-detection-and-notifications"></a>変更の検出と通知

各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、エンティティに加えられた変更を追跡します。 これらの追跡対象エンティティは、が呼び出されると、データベースへの変更を <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 行います。 これについては、 [EF Core の Change Tracking](xref:core/change-tracking/index)に記載されています。このドキュメントでは、エンティティの状態と Entity Framework Core (EF Core) の変更の追跡の基礎を理解していることを前提としています。

プロパティとリレーションシップの変更を追跡するには、DbContext でこれらの変更を検出できる必要があります。 このドキュメントでは、この検出がどのように行われるかについて説明します。また、プロパティ通知または変更追跡プロキシを使用して、変更を直ちに検出する方法についても説明します。

> [!TIP]
> [GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。

## <a name="snapshot-change-tracking"></a>スナップショットによる変更の追跡

既定では、EF Core は、DbContext インスタンスによって最初に追跡されるときに、すべてのエンティティのプロパティ値のスナップショットを作成します。 このスナップショットに格納されている値は、変更されたプロパティ値を特定するために、エンティティの現在の値と比較されます。

この変更の検出は、データベースに更新を送信する前に、変更されたすべての値が検出されるように SaveChanges が呼び出されたときに発生します。 ただし、変更の検出も、アプリケーションが最新の追跡情報を操作していることを確認するために、他のタイミングでも行われます。 変更の検出は、を呼び出すことによっていつでも強制でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。

### <a name="when-change-detection-is-needed"></a>変更の検出が必要な場合

_この変更を行うために EF Core を使用せず_ にプロパティまたはナビゲーションを変更した場合は、変更の検出が必要です。 たとえば、ブログや投稿を読み込んで、次のようなエンティティを変更することを検討してください。

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

を呼び出す前に [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 表示すると、変更が検出されていないため、エンティティの状態および変更されたプロパティデータに反映されません。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

具体的には、ブログエントリの状態は依然として、 `Unchanged` 新しい投稿は追跡対象エンティティとして表示されません。 (EF Core によってこれらの変更がまだ検出されていない場合でも、ずる賢いはプロパティに新しい値を報告します。 これは、デバッグビューがエンティティインスタンスから現在の値を直接読み取るためです。)

検出された変更を呼び出した後のデバッグビューと比較します。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

これで、ブログはとして正しくマークされ、 `Modified` 新しい投稿が検出され、として追跡されるようになりました `Added` 。

このセクションの冒頭では、EF Core を使用して _変更を行う_ のではなく、変更の検出が必要であることを説明しています。 これは、上記のコードで発生しています。 つまり、プロパティおよびナビゲーションへの変更は、EF Core メソッドを使用するのではなく、 _エンティティインスタンスに対して直接_ 行われます。

これを次のコードと比較して、同じようにエンティティを変更しますが、今回は EF Core メソッドを使用します。

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

この場合、変更トラッカーデバッグビューは、変更の検出が行われていなくても、すべてのエンティティの状態とプロパティの変更が判明していることを示しています。 これは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> が EF Core メソッドであるためです。これは EF Core が、このメソッドによって行われた変更をすぐに認識できることを意味します。 同様に、を呼び出すことで、 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 新しいエンティティに関する情報をすぐに把握し、適切に追跡 EF Core ことができます。

> [!TIP]
> エンティティの変更を行うために常に EF Core メソッドを使用して、変更を検出しないようにしてください。 多くの場合、これを行うと、通常の方法でエンティティを変更するよりも煩雑になり、パフォーマンスが低下します。 このドキュメントでは、変更が必要であることを検出するタイミングと、そうでない場合について説明します。 その目的は、変更の検出を回避することではありません。

### <a name="methods-that-automatically-detect-changes"></a>変更を自動的に検出するメソッド

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> は、メソッドによって自動的に呼び出され、結果に影響を与える可能性があります。 それらの方法を次に示します。

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> また <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> 、データベースを更新する前にすべての変更が検出されるようにします。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>エンティティの状態および変更されたプロパティが最新であることを確認します。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>。結果が正確であることを確認します。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>。連鎖する前にプリンシパル/親エンティティのエンティティの状態を正しく保つために使用します。
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>。追跡対象のグラフが最新であることを確認します。

また、追跡されるエンティティのグラフ全体ではなく、1つのエンティティインスタンスでのみ変更の検出が行われる場所もあります。 次のような場所があります。

- を使用する場合 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> 、エンティティの状態と変更されたプロパティが最新であることを確認します。
- 、、またはなどのメソッドを使用して、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> `Property` プロパティの `Collection` `Reference` `Member` 変更、現在の値などを確実に最新の状態にする場合。
- 必要なリレーションシップが切断されているために依存/子エンティティを削除する場合。 これは、エンティティが再親されたために削除されないことを検出します。

を呼び出すことにより、1つのエンティティに対する変更のローカル検出を明示的にトリガーでき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> ます。

> [!NOTE]
> ローカル検出の変更によって、完全な検出によって検出されるいくつかの変更が失われる可能性があります。 これは、他のエンティティに対する検出されていない変更によって発生する連鎖アクションが、対象のエンティティに影響を与える場合に発生します。 このような場合、アプリケーションは、明示的にを呼び出すことによって、すべてのエンティティのフルスキャンを強制的に実行する必要があり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。

### <a name="disabling-automatic-change-detection"></a>自動変更検出の無効化

ほとんどのアプリケーションでは、変更の検出のパフォーマンスがボトルネックになることはありません。 ただし、変更を検出すると、何千ものエンティティを追跡する一部のアプリケーションでパフォーマンスの問題になる可能性があります。 (正確な数値は、エンティティ内のプロパティの数など、多くの点に依存しています)。このため、を使用して、変更の自動検出を無効にすることができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> ます。 たとえば、ペイロードとの多対多リレーションシップで結合エンティティを処理することを検討してください。

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

前のセクションで説明したように、との両方で <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 自動的に変更が検出されます。 ただし、エントリを呼び出した後、コードでエンティティまたはプロパティの状態が変更されることはありません。 (追加されたエンティティに通常のプロパティ値を設定しても、状態の変更は発生しません)。したがって、このコードでは、ベースの SaveChanges メソッドを呼び出すときに、不要な自動変更検出が無効になります。 また、このコードでは、SaveChanges が失敗した場合でも、既定の設定が復元されるように、try/finally ブロックを使用します。

> [!TIP]
> コードで自動変更検出を無効にして適切に実行する必要があると想定しないでください。 これは、アプリケーションをプロファイリングする場合にのみ必要です。多くのエンティティを追跡することは、変更検出のパフォーマンスが問題であることを示します。

### <a name="detecting-changes-and-value-conversions"></a>変更と値の変換の検出

エンティティ型のスナップショット変更の追跡を使用するには、EF Core 次のことが可能である必要があります。

- エンティティが追跡されるときに各プロパティ値のスナップショットを作成する
- この値をプロパティの現在の値と比較します。
- 値のハッシュコードを生成します

これは、データベースに直接マップできる型の EF Core によって自動的に処理されます。 ただし、 [値コンバーターを使用](xref:core/modeling/value-conversions)してプロパティをマップする場合、そのコンバーターは、これらのアクションの実行方法を指定する必要があります。 これは、値の比較子を使用して実現されます。詳細については、 [値](xref:core/modeling/value-comparers) の比較子のドキュメントを参照してください。

## <a name="notification-entities"></a>通知エンティティ

ほとんどのアプリケーションでは、スナップショットの変更の追跡をお勧めします。 ただし、多数のエンティティを追跡するアプリケーションや、それらのエンティティに多くの変更を加えるアプリケーションでは、プロパティとナビゲーション値が変更されたときに EF Core に自動的に通知するエンティティを実装する方がメリットがあります。 これらは "通知エンティティ" と呼ばれます。

### <a name="implementing-notification-entities"></a>通知エンティティの実装

通知エンティティ <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> は、.net 基本クラスライブラリ (BCL) の一部であるおよびインターフェイスを使用します。 これらのインターフェイスは、プロパティ値の変更前後に発生する必要があるイベントを定義します。 例:

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

また、コレクションのナビゲーションでは、を実装する必要があります。 `INotifyCollectionChanged` 上記の例では、投稿のを使用してこれを満たしています。 <xref:System.Collections.ObjectModel.ObservableCollection%601> また EF Core には、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> 安定した順序でより効率的な参照を持つ実装が付属しています。

通常、この通知コードのほとんどは、マップされていない基本クラスに移動されます。 例:

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>通知エンティティの構成

`INotifyPropertyChanging` `INotifyPropertyChanged` EF Core で使用するために完全に実装されているかどうかを検証 EF Core する方法はありません。 特に、これらのインターフェイスの一部の用途では、EF Core で必要とされるすべてのプロパティ (ナビゲーションを含む) ではなく、特定のプロパティに対してのみ通知を行います。 このため、EF Core はこれらのイベントに自動的にフックしません。

代わりに、これらの通知エンティティを使用するように EF Core を構成する必要があります。 通常、これは、を呼び出すことによって、すべてのエンティティ型に対して行われ <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ます。 例:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

(を使用して、さまざまなエンティティ型に対して方法を異なる方法で設定することもでき <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ますが、これは通常、通知エンティティではない型に対しても検出の変更が必要になるためです)。

完全な通知変更の追跡を行うに `INotifyPropertyChanging` は、との両方が実装されている必要があり `INotifyPropertyChanged` ます。 これにより、プロパティ値が変更される直前に元の値を保存できるため、エンティティを追跡するときにスナップショットを作成する EF Core 必要がなくなります。 を実装するエンティティ型は、 `INotifyPropertyChanged` EF Core と共に使用することもできます。 この場合、EF では、元の値を追跡するためにエンティティを追跡するときにスナップショットが作成されますが、その後、通知を使用して変更をすぐに検出し、検出された変更を呼び出す必要はありません。

次の <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> 表に、異なる値をまとめて示します。

| ChangeTrackingStrategy                              | 必要なインターフェイス                                      | 検出の変更が必要 | スナップショットの元の値
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| スナップショット                                            | None                                                   | はい                 | はい
| 警告の通知                                | INotifyPropertyChanged                                 | いいえ                  | はい
| すべての通知の方法                     | INotifyPropertyChanged と INotifyPropertyChanging     | いいえ                  | いいえ
| ChangingAndChangedNotificationsWithOriginalValues   | INotifyPropertyChanged と INotifyPropertyChanging     | いいえ                  | はい

### <a name="using-notification-entities"></a>通知エンティティの使用

通知エンティティは他のエンティティと同様に動作しますが、エンティティインスタンスを変更するときに、これらの変更を検出するためにを呼び出す必要がない点が異なり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。 例:

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

通常のエンティティの場合、 [変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views) では、検出された変更が呼び出されるまでこれらの変更が検出されなかったことが示されていました。 通知エンティティが使用されているときにデバッグビューを見ると、これらの変更がすぐに検出されていることがわかります。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>変更追跡のプロキシ

> [!NOTE]
> 変更追跡プロキシは EF Core 5.0 で導入されました。

EF Core は、およびを実装するプロキシ型を動的に生成でき <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> ます。 これには、次の例のように、 [Microsoft の EntityFrameworkCore プロキシ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)NuGet パッケージをインストールし、変更追跡プロキシを有効にする必要があります。 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

動的プロキシを作成するには、エンティティ型から継承する新しい動的な .NET 型を作成し、その後、すべてのプロパティ setter をオーバーライドし[ます。](https://www.nuget.org/packages/Castle.Core/) したがって、プロキシのエンティティ型は、から継承できる型である必要があり、オーバーライドできるプロパティを持つ必要があります。 また、明示的に作成されたコレクションのナビゲーションは、次のように実装する必要があり <xref:System.Collections.Specialized.INotifyCollectionChanged> ます。

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

変更追跡プロキシの重要な欠点の1つは、EF Core は常に、基になるエンティティ型のインスタンスではなく、プロキシのインスタンスを追跡する必要があることです。 これは、基になるエンティティ型のインスタンスが通知を生成しないためです。つまり、これらのエンティティに対して行われた変更が失われることを意味します。

EF Core は、データベースに対してクエリを実行するときにプロキシインスタンスを自動的に作成するため、この欠点は、通常、新しいエンティティインスタンスの追跡に限定されます。 これらのインスタンスは、 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> を使用する通常の方法では **なく** 、拡張メソッドを使用して作成する必要があり `new` ます。 これは、前の例のコードでを使用する必要があることを意味し `CreateProxy` ます。

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>変更の追跡イベント

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType>エンティティが初めて追跡されたときにイベントを発生させる EF Core ます。 将来のエンティティの状態の変更によって、イベントが発生 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> します。 詳細については、「[EF Core での .NET イベント](xref:core/logging-events-diagnostics/events)」を参照してください。

> [!NOTE]
> `StateChanged`状態がから `Detached` 他の状態に変更された場合でも、エンティティが最初に追跡されるときにイベントは発生しません。 イベントとイベントの両方をリッスンして、関連するすべての通知を取得するようにしてください `StateChanged` `Tracked` 。
