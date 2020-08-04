---
title: DbContext の構成-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 9614449f6ead393b514f42b718b4cae5f97dfc98
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526421"
---
# <a name="configuring-a-dbcontext"></a>DbContext の構成

この記事では、を使用してを構成し、 `DbContext` `DbContextOptions` 特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するための基本的なパターンについて説明します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時の DbContext 構成

[移行](xref:core/managing-schemas/migrations/index)などのデザイン時ツールでは、 `DbContext` アプリケーションのエンティティ型についての詳細を収集し、データベーススキーマにマップする方法について、型の動作するインスタンスを検出して作成できる必要があります。 EF Core このプロセスは、実行時に構成するのと同じように構成されるように、ツールがを簡単に作成できる限り、自動的に行うことができます `DbContext` 。

に必要な構成情報を提供するパターンは `DbContext` 実行時に動作しますが、デザイン時にを使用する必要があるツール `DbContext` は、限られた数のパターンでのみ機能します。 これらの詳細については、「[デザイン時コンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)」セクションで説明します。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions の構成

`DbContext``DbContextOptions`任意の処理を実行するには、のインスタンスが必要です。 インスタンスには、次のよう `DbContextOptions` な構成情報が含まれます。

- 使用するデータベースプロバイダー。通常は、やなどのメソッドを呼び出すことによって選択され `UseSqlServer` `UseSqlite` ます。 これらの拡張メソッドには、やなどの対応するプロバイダーパッケージが必要です `Microsoft.EntityFrameworkCore.SqlServer` `Microsoft.EntityFrameworkCore.Sqlite` 。 メソッドは、名前空間で定義されて `Microsoft.EntityFrameworkCore` います。
- データベースインスタンスの必要な接続文字列または識別子。通常は、上で説明したプロバイダー選択メソッドに引数として渡されます。
- プロバイダーレベルの任意の動作セレクター。通常は、プロバイダーの選択メソッドの呼び出しの内側にもチェーンされます。
- 一般的な EF Core 動作セレクター。通常は、プロバイダーセレクターメソッドの後または前にチェーンされます。

次の例では、 `DbContextOptions` SQL Server プロバイダー、変数に含まれる接続、 `connectionString` プロバイダーレベルのコマンドタイムアウト、および既定ではすべてのクエリを `DbContext` [非追跡](xref:core/querying/tracking#no-tracking-queries)で実行する EF Core 動作セレクターを使用するようにを構成します。

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 前に説明したプロバイダーセレクターメソッドとその他の動作セレクターメソッドは、 `DbContextOptions` またはプロバイダー固有のオプションクラスの拡張メソッドです。 これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間 (通常は) が必要であり、 `Microsoft.EntityFrameworkCore` プロジェクトにパッケージの依存関係が追加されている必要があります。

は、 `DbContextOptions` メソッドをオーバーライドする `DbContext` か、 `OnConfiguring` コンストラクター引数を使用して外部からに渡すことによってに渡すことができます。

両方が使用されている場合、は最後に適用され、 `OnConfiguring` コンストラクター引数に指定されたオプションを上書きできます。

### <a name="constructor-argument"></a>コンストラクター引数

コンストラクターは、次のようにを受け入れるだけです `DbContextOptions` 。

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> DbContext の基本コンストラクターは、非ジェネリックバージョンのを受け取ることもでき `DbContextOptions` ますが、複数のコンテキストタイプを持つアプリケーションでは、非ジェネリックバージョンを使用することは推奨されません。

これで、アプリケーションは、コンテキストをインスタンス化するときに、次のようにを渡すことができます `DbContextOptions` 。

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

コンテキスト自体でを初期化することもでき `DbContextOptions` ます。 この手法は基本的な構成に使用できますが、通常は、データベース接続文字列など、外部から特定の構成の詳細を取得する必要があります。 これは、構成 API またはその他の方法で行うことができます。

コンテキスト内で初期化するには、 `DbContextOptions` メソッドをオーバーライド `OnConfiguring` し、指定されたでメソッドを呼び出し `DbContextOptionsBuilder` ます。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

アプリケーションは、コンストラクターに何も渡さずにこのようなコンテキストをインスタンス化できます。

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> この方法は、テストが完全なデータベースを対象としている場合を除き、テストには適していません。

### <a name="using-dbcontext-with-dependency-injection"></a>依存関係の挿入での DbContext の使用

EF Core `DbContext` は、依存関係挿入コンテナーでのの使用をサポートしています。 DbContext 型は、メソッドを使用してサービスコンテナーに追加でき `AddDbContext<TContext>` ます。

`AddDbContext<TContext>`は、DbContext 型、 `TContext` 、および対応するを、 `DbContextOptions<TContext>` サービスコンテナーからの挿入に使用できるようにします。

依存関係の挿入の詳細については[、以下を](#more-reading)参照してください。

`DbContext`を依存関係の挿入に追加します。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

これを行うには、を受け入れる DbContext 型に[コンストラクター引数](#constructor-argument)を追加する必要があり `DbContextOptions<TContext>` ます。

コンテキストコード:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

アプリケーションコード (ASP.NET Core):

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

アプリケーションコード (ServiceProvider を直接、あまり一般的ではない):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>DbContext スレッドの問題の回避

Entity Framework Core では、同じインスタンスで実行されている複数の並列操作はサポートされていません `DbContext` 。 これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。 したがって、常に `await` 非同期呼び出しをすぐに `DbContext` 実行するか、並列で実行される操作に個別のインスタンスを使用します。

EF Core がインスタンスを同時に使用しようとしたことを検出すると、次のようなメッセージが表示され `DbContext` `InvalidOperationException` ます。

> 前の操作が完了する前に、このコンテキストで2番目の操作が開始されました。 これは通常、同じ DbContext のインスタンスを使用する異なるスレッドによって発生しますが、インスタンスメンバーはスレッドセーフであるとは限りません。

同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。

同じインスタンスに対して、誤って同時アクセスを引き起こす可能性がある一般的な誤りがあり `DbContext` ます。

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していません

非同期メソッドを使用すると、ブロックしない方法でデータベースにアクセスする操作を開始 EF Core ことができます。 ただし、呼び出し元がこれらのメソッドのいずれかの完了を待機しておらず、に対して他の操作を実行しようとした場合、 `DbContext` の状態は `DbContext` (おそらく) 破損している可能性があります。

常に非同期メソッド EF Core 待機します。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)拡張メソッドは、 `DbContext` 既定でスコープを持つ[有効期間](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)を持つ型を登録します。

これは、特定の時点で各クライアント要求を実行するスレッドが1つだけであり、各要求が個別の依存関係挿入スコープ (したがって別のインスタンス) を取得するため、ほとんどの ASP.NET Core アプリケーションの同時アクセスの問題から安全です `DbContext` 。 Blazor Server ホスティングモデルでは、Blazor ユーザー回線を維持するために1つの論理要求が使用されます。そのため、既定の挿入スコープが使用されている場合は、ユーザー回線ごとに1つのスコープされた DbContext インスタンスのみ使用できます。

複数のスレッドを明示的に並列実行するコードは、インスタンスに同時にアクセスされないようにする必要があり `DbContext` ます。

依存関係の挿入を使用すると、コンテキストをスコープとして登録し、各スレッドに対して (を使用して) スコープを作成するか `IServiceScopeFactory` 、を `DbContext` (パラメーターを受け取るのオーバーロードを使用して) 一時的に登録することによって実現でき `AddDbContext` `ServiceLifetime` ます。

## <a name="more-reading"></a>その他の参考資料

- DI の使用方法の詳細については、「[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)」を参照してください。
- 詳細については、「[テスト](testing/index.md)」を参照してください。
