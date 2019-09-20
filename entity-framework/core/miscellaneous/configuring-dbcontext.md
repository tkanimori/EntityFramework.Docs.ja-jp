---
title: DbContext の構成-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 734acad86e364abbfd1522fe79d4a847b1acfb52
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149038"
---
# <a name="configuring-a-dbcontext"></a>DbContext の構成

この記事では、を使用し`DbContext`てを`DbContextOptions`構成し、特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するための基本的なパターンについて説明します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時の DbContext 構成

[移行](xref:core/managing-schemas/migrations/index)などのデザイン時ツールでは、アプリケーションのエンティティ型についての詳細を収集し`DbContext` 、データベーススキーマにマップする方法について、型の動作するインスタンスを検出して作成できる必要があります。 EF Core このプロセスは、実行時に構成するのと同じように`DbContext`構成されるように、ツールがを簡単に作成できる限り、自動的に行うことができます。

に`DbContext`必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時`DbContext`にを使用する必要があるツールは、限られた数のパターンでのみ機能します。 これらの詳細については、「[デザイン時コンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)」セクションで説明します。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions の構成

`DbContext`任意の処理を実行`DbContextOptions`するには、のインスタンスが必要です。 インスタンス`DbContextOptions`には、次のような構成情報が含まれます。

- 使用するデータベースプロバイダー。通常は、や`UseSqlServer` `UseSqlite`などのメソッドを呼び出すことによって選択されます。 これらの拡張メソッドに`Microsoft.EntityFrameworkCore.SqlServer`は、や`Microsoft.EntityFrameworkCore.Sqlite`などの対応するプロバイダーパッケージが必要です。 メソッドは、 `Microsoft.EntityFrameworkCore`名前空間で定義されています。
- データベースインスタンスの必要な接続文字列または識別子。通常は、上で説明したプロバイダー選択メソッドに引数として渡されます。
- プロバイダーレベルの任意の動作セレクター。通常は、プロバイダーの選択メソッドの呼び出しの内側にもチェーンされます。
- 一般的な EF Core 動作セレクター。通常は、プロバイダーセレクターメソッドの後または前にチェーンされます。

次の例では`DbContextOptions` 、SQL Server プロバイダー、 `connectionString`変数に含まれる接続、プロバイダーレベルのコマンドタイムアウト、および`DbContext`すべてのクエリを実行するための EF Core の動作セレクターを使用するようにを構成します。既定では[、追跡されません](xref:core/querying/tracking#no-tracking-queries)。

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 前に説明したプロバイダーセレクターメソッドとその他の動作セレクター `DbContextOptions`メソッドは、またはプロバイダー固有のオプションクラスの拡張メソッドです。 これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間`Microsoft.EntityFrameworkCore`(通常は) が必要であり、プロジェクトにパッケージの依存関係が追加されている必要があります。

は、 `OnConfiguring`メソッドをオーバーライドする`DbContext`か、コンストラクター引数を使用して外部からに渡すことによってに渡すことができます。`DbContextOptions`

両方が使用され`OnConfiguring`ている場合、は最後に適用され、コンストラクター引数に指定されたオプションを上書きできます。

### <a name="constructor-argument"></a>コンストラクター引数

コンストラクターを使用したコンテキストコード:

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
> Dbcontext の基本コンストラクターは、非ジェネリックバージョンの`DbContextOptions`を受け取ることもできますが、複数のコンテキストタイプを持つアプリケーションでは、非ジェネリックバージョンを使用することは推奨されません。

コンストラクター引数から初期化するアプリケーションコード:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

コンテキストコード`OnConfiguring`:

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

を使用`DbContext` `OnConfiguring`するを初期化するアプリケーションコード:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> この方法は、テストが完全なデータベースを対象としている場合を除き、テストには適していません。

### <a name="using-dbcontext-with-dependency-injection"></a>依存関係の挿入での DbContext の使用

EF Core は、 `DbContext`依存関係挿入コンテナーでのの使用をサポートしています。 Dbcontext 型は、 `AddDbContext<TContext>`メソッドを使用してサービスコンテナーに追加できます。

`AddDbContext<TContext>`は、dbcontext 型、 `TContext`、および対応する`DbContextOptions<TContext>`を、サービスコンテナーからの挿入に使用できるようにします。

依存関係の挿入の詳細については[、以下を](#more-reading)参照してください。

を依存`DbContext`関係の挿入に追加します。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

これを行うには、を受け入れる`DbContextOptions<TContext>`dbcontext 型に[コンストラクター引数](#constructor-argument)を追加する必要があります。

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

Entity Framework Core では、同じ`DbContext`インスタンスで実行されている複数の並列操作はサポートされていません。 これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。 したがって、 `await`常に非同期呼び出しをすぐに`DbContext`実行するか、並列で実行される操作に個別のインスタンスを使用します。

EF Core がインスタンスを`DbContext`同時に使用しようとしたことを検出すると、次のようなメッセージが`InvalidOperationException`表示されます。 

> 前の操作が完了する前に、このコンテキストで2番目の操作が開始されました。 これは通常、同じ DbContext のインスタンスを使用する異なるスレッドによって発生しますが、インスタンスメンバーはスレッドセーフであるとは限りません。

同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。

同じ`DbContext`インスタンスに対して、誤って同時アクセスを引き起こす可能性がある一般的な誤りがあります。

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していません

非同期メソッドを使用すると、ブロックしない方法でデータベースにアクセスする操作を開始 EF Core ことができます。 ただし、呼び出し元がこれらのメソッドのいずれかの完了を待機しておらず、 `DbContext`に対して他の操作を実行しようとした場合、 `DbContext`の状態は (おそらく) 破損している可能性があります。 

常に非同期メソッド EF Core 待機します。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する

拡張[`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)メソッドは、 `DbContext`既定でスコープを持つ[有効期間](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)を持つ型を登録します。 

これは、特定の時点で各クライアント要求を実行するスレッドが1つしかないため、ASP.NET Core アプリケーションの同時アクセスの問題から安全です。また、各要求は個別の`DbContext`依存関係挿入スコープ (したがって、インスタンス)。

ただし、複数のスレッドを明示的に並列実行するコード`DbContext`は、インスタンスに同時にアクセスされないようにする必要があります。

依存関係の挿入を使用すると、コンテキストをスコープとして登録し、各スレッド`IServiceScopeFactory`に対して (を使用して) `DbContext`スコープを作成するか、を`AddDbContext`一時的に登録することによって実現できます (のオーバーロードを使用します。`ServiceLifetime`パラメーター)。

## <a name="more-reading"></a>その他の参考資料

* DI の使用方法の詳細については、「[依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)」を参照してください。
* 詳細については、「[テスト](testing/index.md)」を参照してください。
