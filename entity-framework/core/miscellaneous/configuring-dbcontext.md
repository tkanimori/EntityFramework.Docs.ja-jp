---
title: DbContext の構成-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: e04c1b65df96819f3493e0ed34ccf26609511f6a
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445909"
---
# <a name="configuring-a-dbcontext"></a>DbContext の構成

この記事では、特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するために `DbContextOptions` を介して @no__t 0 を構成するための基本的なパターンについて説明します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時の DbContext 構成

[移行](xref:core/managing-schemas/migrations/index)などのデザイン時ツールでは、アプリケーションのエンティティ型についての詳細情報を収集し、データベーススキーマにマップする方法について、`DbContext` 型の動作するインスタンスを検出して作成できる必要があり EF Core。 このプロセスは、実行時に構成するのと同じように構成されるように、ツールで簡単に @no__t を作成できる限り、自動的に行うことができます。

@No__t 0 に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。 これらの詳細については、「[デザイン時コンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)」セクションで説明します。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions の構成

`DbContext` には、すべての作業を実行するために `DbContextOptions` のインスタンスが必要です。 @No__t 0 のインスタンスは、次のような構成情報を保持します。

- 使用するデータベースプロバイダー。通常は、`UseSqlServer` や `UseSqlite` などのメソッドを呼び出すことによって選択されます。 これらの拡張メソッドには、対応するプロバイダーパッケージ (`Microsoft.EntityFrameworkCore.SqlServer` や `Microsoft.EntityFrameworkCore.Sqlite` など) が必要です。 メソッドは `Microsoft.EntityFrameworkCore` 名前空間で定義されています。
- データベースインスタンスの必要な接続文字列または識別子。通常は、上で説明したプロバイダー選択メソッドに引数として渡されます。
- プロバイダーレベルの任意の動作セレクター。通常は、プロバイダーの選択メソッドの呼び出しの内側にもチェーンされます。
- 一般的な EF Core 動作セレクター。通常は、プロバイダーセレクターメソッドの後または前にチェーンされます。

次の例では、SQL Server プロバイダー、`connectionString` 変数に含まれる接続、プロバイダーレベルのコマンドタイムアウト、およびすべてのクエリを @no__t [-2 で](xref:core/querying/tracking#no-tracking-queries)実行する EF Core 動作セレクターを使用するように `DbContextOptions` を構成します。既定では、次のようになります。

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 前に説明したプロバイダーセレクターメソッドとその他の動作セレクターメソッドは、@no__t 0 またはプロバイダー固有のオプションクラスの拡張メソッドです。 これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間 (通常は @no__t 0) が必要であり、プロジェクトにパッケージの依存関係が追加されている必要があります。

@No__t-0 は、`OnConfiguring` メソッドをオーバーライドするか、コンストラクター引数を使用して外部から `DbContext` に渡すことができます。

両方が使用されている場合は、`OnConfiguring` が最後に適用され、コンストラクター引数に指定されたオプションを上書きできます。

### <a name="constructor-argument"></a>コンストラクター引数

コンストラクターは、次のように @no__t 0 を受け入れるだけです。

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
> DbContext の基本コンストラクターは、非ジェネリックバージョンの `DbContextOptions` も受け入れますが、複数のコンテキストタイプを持つアプリケーションでは、非ジェネリックバージョンを使用することは推奨されません。

これで、アプリケーションは、次のように、コンテキストをインスタンス化するときに @no__t 0 を渡すことができます。

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

また、コンテキスト自体で @no__t 0 を初期化することもできます。 この手法は基本的な構成に使用できますが、通常は、データベース接続文字列など、外部から特定の構成の詳細を取得する必要があります。 これは、構成 API またはその他の方法で行うことができます。

コンテキスト内で `DbContextOptions` を初期化するには、`OnConfiguring` メソッドをオーバーライドし、指定された `DbContextOptionsBuilder` でメソッドを呼び出します。

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

EF Core は、依存関係挿入コンテナーでの `DbContext` の使用をサポートしています。 DbContext 型は、`AddDbContext<TContext>` メソッドを使用してサービスコンテナーに追加できます。

`AddDbContext<TContext>` にすると、サービスコンテナーからの挿入に使用できる DbContext 型、`TContext`、および対応する `DbContextOptions<TContext>` の両方が作成されます。

依存関係の挿入の詳細については[、以下を](#more-reading)参照してください。

@No__t-0 を依存関係の挿入に追加します。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

そのためには、`DbContextOptions<TContext>` を受け入れる[コンストラクター引数](#constructor-argument)を dbcontext 型に追加する必要があります。

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

Entity Framework Core では、同じ `DbContext` インスタンスで実行される複数の並列操作はサポートされていません。 これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。 したがって、常に `await` の非同期呼び出しを直ちに実行するか、並列で実行される操作に個別の `DbContext` インスタンスを使用します。

EF Core が @no__t 0 のインスタンスを同時に使用しようとしたことを検出すると、次のようなメッセージが表示された `InvalidOperationException` が表示されます。 

> 前の操作が完了する前に、このコンテキストで2番目の操作が開始されました。 これは通常、同じ DbContext のインスタンスを使用する異なるスレッドによって発生しますが、インスタンスメンバーはスレッドセーフであるとは限りません。

同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。

同じ @no__t 0 インスタンスで、誤って同時アクセスを引き起こす可能性がある一般的な誤りがあります。

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していません

非同期メソッドを使用すると、ブロックしない方法でデータベースにアクセスする操作を開始 EF Core ことができます。 しかし、呼び出し元がこれらのメソッドのいずれかの完了を待機しておらず、`DbContext` に対して他の操作を実行しようとすると、@no__t の状態が (おそらく) 破損している可能性があります。 

常に非同期メソッド EF Core 待機します。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する

[@No__t-1](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)拡張メソッドは、既定で[スコープ有効期間](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)が指定された @no__t 型を登録します。 

これは、特定の時間に各クライアント要求を実行するスレッドが1つしかないため、ASP.NET Core アプリケーションの同時アクセスの問題から安全です。また、各要求は個別の依存関係挿入スコープ (したがって、個別の @no__t 0 インスタンス) を取得するためです。

ただし、複数のスレッドを明示的に並列実行するコードでは、@no__t 0 のインスタンスに同時にアクセスしないようにする必要があります。

依存関係の挿入を使用すると、コンテキストをスコープとして登録するか、各スレッドに対して (`IServiceScopeFactory` を使用して) スコープを作成するか、または `DbContext` を transient として登録することによって実現できます (`ServiceLifetime` パラメーターを受け取る `AddDbContext` のオーバーロードを使用します)。

## <a name="more-reading"></a>その他の参考資料

* DI の使用方法の詳細については、「[依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)」を参照してください。
* 詳細については、「[テスト](testing/index.md)」を参照してください。
