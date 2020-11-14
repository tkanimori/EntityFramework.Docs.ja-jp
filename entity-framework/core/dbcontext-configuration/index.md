---
title: DbContext の構成 - EF Core
description: Entity Framework Core を使用して DbContext を構成するための戦略
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431132"
---
# <a name="configuring-a-dbcontext"></a>DbContext の構成

この記事では、特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するために、`DbContextOptions` を使用して `DbContext` を構成するための基本パターンについて説明します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時の DbContext の構成

[移行](xref:core/managing-schemas/migrations/index)などの EF Core デザイン時ツールでは、アプリケーションのエンティティ型とそれらがデータベース スキーマにどのようにマップされるかに関する詳細情報を収集するため、`DbContext` 型の作業インスタンスを検出および作成できる必要があります。 このプロセスは、実行時に構成される場合と同様の方法で構成されるように `DbContext` をツールで簡単に作成できる限り、自動的に行われます。

`DbContext` に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。 これらについては、[「Design-Time Context Creation」(デザイン時のコンテキストの作成)](xref:core/cli/dbcontext-creation) で詳しく説明します。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions の構成

`DbContext` で何らかの作業を実行するには、`DbContextOptions` のインスタンスが必要です。 `DbContextOptions` インスタンスには、次のような構成情報が格納されます。

- 使用するデータベース プロバイダー。通常は、`UseSqlServer` や `UseSqlite` などのメソッドを呼び出すことで選択されます。 これらの拡張メソッドには、`Microsoft.EntityFrameworkCore.SqlServer` や `Microsoft.EntityFrameworkCore.Sqlite` などの対応するプロバイダー パッケージが必要です。 これらのメソッドは `Microsoft.EntityFrameworkCore` 名前空間で定義されています。
- データベース インスタンスの必要な接続文字列または識別子。通常は、上記のプロバイダー選択メソッドに引数として渡されます
- プロバイダー レベルのオプションの動作セレクター。通常は、プロバイダー選択メソッドの呼び出しの内部にもチェーンされます
- 一般的な EF Core の動作セレクター。通常は、プロバイダー セレクター メソッドの呼び出しの前または後にチェーンされます

次の例では、SQL Server プロバイダー、`connectionString` 変数に含まれる接続、プロバイダーレベルのコマンド タイムアウト、および `DbContext` で実行されるすべてのクエリを既定で[追跡なし](xref:core/querying/tracking#no-tracking-queries)にする EF Core の動作セレクターを使用するように、`DbContextOptions` を構成します。

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> 上記のプロバイダー セレクター メソッドとその他の動作セレクター メソッドは、`DbContextOptions` またはプロバイダー固有のオプション クラスの拡張メソッドです。 これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間 (通常は `Microsoft.EntityFrameworkCore`) があり、プロジェクトに追加のパッケージの依存関係が含まれている必要があります。

`DbContextOptions` を `DbContext` に渡すには、`OnConfiguring` メソッドをオーバーライドするか、外部でコンストラクター引数を使用します。

両方を使用すると、`OnConfiguring` の方が後で適用され、コンストラクター引数に渡されたオプションをオーバーライドできます。

### <a name="constructor-argument"></a>コンストラクター引数

コンストラクターは、次のように `DbContextOptions` をそのまま受け入れることができます。

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> DbContext の基本コンストラクターは非ジェネリック バージョンの `DbContextOptions` も受け入れますが、複数のコンテキスト型を持つアプリケーションで非ジェネリック バージョンを使用することはお勧めしません。

これで、アプリケーションはコンテキストをインスタンス化するときに、次のように `DbContextOptions` を渡すことができます。

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

コンテキスト自体の内部で `DbContextOptions` を初期化することもできます。 この手法は基本的な構成で使用できますが、その場合でも、通常は外部から特定の構成の詳細 (データベース接続文字列など) を取得する必要があります。 これは、構成 API またはその他の手段を使用して行うことができます。

コンテキスト内で `DbContextOptions` を初期化するには、`OnConfiguring` メソッドをオーバーライドし、指定された `DbContextOptionsBuilder` のメソッドを呼び出します。

```csharp
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

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> この方法は、テストが完全なデータベースを対象としている場合を除き、テストには適していません。

### <a name="using-dbcontext-with-dependency-injection"></a>依存関係の挿入での DbContext の使用

EF Core では、依存関係挿入コンテナーでの `DbContext` の使用がサポートされています。 DbContext 型をサービス コンテナーに追加するには、`AddDbContext<TContext>` を使用します。

`AddDbContext<TContext>` を使用すると、DbContext 型 `TContext` と対応する `DbContextOptions<TContext>` の両方をサービス コンテナーからの挿入に使用できるようになります。

依存関係の挿入の詳細については、下記の「[関連項目](#more-reading)」を参照してください。

`DbContext` を依存関係の挿入に追加します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

そのためには、`DbContextOptions<TContext>` を受け入れる DbContext 型に[コンストラクター引数](#constructor-argument)を追加する必要があります。

コード コンテキスト:

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

アプリケーション コード (ASP.NET Core 内):

```csharp
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

アプリケーション コード (ServiceProvider を直接使用、あまり一般的ではない):

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>DbContext のスレッド処理の問題を回避する

Entity Framework Core では、同じ `DbContext` インスタンス上での複数の並列操作の実行がサポートされていません。 これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。 そのため、常に非同期呼び出しを直ちに `await` するか、並列実行される操作に対して個別の `DbContext` インスタンスを使用します。

`DbContext` インスタンスを同時に使用しようとしたことが EF Core によって検出されると、次のようなメッセージとともに `InvalidOperationException` が表示されます。

> 前の操作が完了する前に、このコンテキストで 2 つ目の操作が開始されました。 これは通常、異なるスレッドで DbContext の同じインスタンスが使用された場合に発生しますが、インスタンスのメンバーがスレッド セーフである保証はありません。

同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。

同じ `DbContext` インスタンスに誤って同時にアクセスする原因として、一般に次のような間違いがあります。

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していない

EF Core で非同期メソッドを使用すると、非ブロッキング モードでデータベースにアクセスする操作を開始できます。 しかし、呼び出し元がこれらのメソッドのいずれかの完了を待機せず、`DbContext` に対して他の操作を実行しようとした場合、`DbContext` の状態は (非常に高い確率で) 破損する可能性があります。

常に EF Core の非同期メソッドを直ちに待機してください。

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 拡張メソッドを使用すると、[スコープ付きの有効期間](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)で `DbContext` 型が登録されます。

この場合、ある時点で個々のクライアント要求を実行するスレッドは 1 つしかなく、各要求が別個の依存関係挿入スコープ (したがって、別個の `DbContext` インスタンス) を取得するため、ほとんどの ASP.NET Core アプリケーションでは同時アクセスの問題は発生しません。 Blazor Server ホスティング モデルでは、Blazor ユーザー回線を維持するために 1 つの論理要求が使用されるため、既定の挿入スコープが使用されている場合は、ユーザー回線ごとにスコープ付きの DbContext インスタンスを 1 つだけ使用できます。

複数のスレッドを明示的に並列実行するコードでは、`DbContext` インスタンスが同時にアクセスされないようにする必要があります。

依存関係の挿入を使用してこれを実現するには、コンテキストをスコープ付きとして登録し、スレッドごとに (`IServiceScopeFactory` を使用して) スコープを作成するか、または (`ServiceLifetime` パラメーターを取る `AddDbContext` のオーバーロードを使用して) `DbContext` を一時的として登録します。

## <a name="more-reading"></a>関連項目

- DI の使用の詳細については、[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)に関する記事をご覧ください。
- テストの詳細については、[テスト](xref:core/testing/index)に関する記事をご覧ください。
