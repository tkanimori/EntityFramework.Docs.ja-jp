---
title: EF Core の DbContext を構成します。
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 316d363d4a1b8a909efc1c32b492280c0d16cb4e
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405206"
---
# <a name="configuring-a-dbcontext"></a>DbContext の構成

この記事では構成するための基本的なパターンを`DbContext`を使用して、`DbContextOptions`特定の EF Core プロバイダーと省略可能な動作を使用してデータベースに接続します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時 DbContext の構成

EF Core のデザイン時ツール[移行](xref:core/managing-schemas/migrations/index)を検出し、作業のインスタンスを作成できる必要があります、`DbContext`型アプリケーションのエンティティ型とデータベース スキーマへの割り当て方法に関する詳細情報を収集するためにします。 このプロセスは、ツールを簡単に作成できる限り、自動で、`DbContext`ことは構成する必要が同様に実行時の構成方法とするようにします。

必要な構成情報を提供する任意のパターンの中に、`DbContext`ランタイムを使用する必要とするツールで作業ができ、`DbContext`デザイン時にのみ使用できますパターンの数に制限します。 これらについては説明について詳しく、[デザイン時のコンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)セクション。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions を構成します。

`DbContext` インスタンスが必要`DbContextOptions`作業を実行するためにします。 `DbContextOptions`インスタンスなどの構成情報を実行します。

- データベース プロバイダーを使用するには、通常などのメソッドを呼び出すことによって選択`UseSqlServer`または`UseSqlite`します。 これらの拡張メソッドは、対応するプロバイダーのパッケージをなど必要`Microsoft.EntityFrameworkCore.SqlServer`または`Microsoft.EntityFrameworkCore.Sqlite`します。 メソッドが定義されている、`Microsoft.EntityFrameworkCore`名前空間。
- 任意の必要な接続文字列またはデータベースのインスタンスの識別子通常に渡される引数として上記のように、プロバイダーの選択メソッド
- 通常、プロバイダーの選択メソッドの呼び出しの内部でチェーンも、任意のプロバイダ レベルの省略可能な動作セレクター
- 通常チェーン プロバイダー セレクター メソッドは前に、または後に、EF Core の一般的な動作セレクターで、

次の例では、`DbContextOptions`に SQL Server プロバイダーを使用する接続が含まれている、`connectionString`変数や、プロバイダー レベルのコマンド タイムアウトで実行されるすべてのクエリを EF Core の動作のセレクター、 `DbContext`[追跡なし](xref:core/querying/tracking#no-tracking-queries)既定。

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> プロバイダー セレクター メソッドと上記で説明した他の動作のセレクター メソッドは、拡張メソッドが上`DbContextOptions`またはプロバイダー固有のオプション クラス。 これらの拡張メソッドが、名前空間が存在する必要がありますにアクセスするために (通常`Microsoft.EntityFrameworkCore`) のスコープを設定して、プロジェクトに追加のパッケージの依存関係を含めます。

`DbContextOptions`を指定すると、`DbContext`オーバーライドすることで、`OnConfiguring`メソッドまたはコンス トラクター引数経由で外部から。

両方を使用する場合`OnConfiguring`が最後に適用され、コンス トラクターの引数に指定したオプションを上書きすることができます。

### <a name="constructor-argument"></a>コンス トラクターの引数

コンス トラクターを持つコンテキスト コード:

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
> DbContext の基本コンス トラクターは、非ジェネリック バージョンのも受け入れる`DbContextOptions`が非ジェネリック バージョンを使用しては複数のコンテキストの型を持つアプリケーションにはお勧めしません。

コンス トラクターの引数から初期化するためにアプリケーション コード:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

コンテキスト コード`OnConfiguring`:

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

初期化するためにアプリケーション コード、`DbContext`を使用して`OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> この方法は適していませんテスト、テスト対象のデータベースの完全な場合を除き、します。

### <a name="using-dbcontext-with-dependency-injection"></a>DbContext を使用して、依存関係の挿入

EF Core のサポートを使用して`DbContext`依存関係の注入コンテナーを使用します。 使用して、サービス コンテナーに DbContext 型を追加することができます、`AddDbContext<TContext>`メソッド。

`AddDbContext<TContext>` 両方の DbContext 型と、`TContext`と、対応する`DbContextOptions<TContext>`サービス コンテナーからのインジェクション用に使用できます。

参照してください[参照](#more-reading)以下の依存関係の挿入の詳細についてはします。

追加、`Dbcontext`依存関係の挿入。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け取る DbContext 型を`DbContextOptions<TContext>`します。

コンテキスト コード:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

アプリケーション コード (ASP.NET Core):

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

アプリケーションのコード (サービス プロバイダーを直接使用、あまり一般的):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a>DbContext のスレッド処理の問題を回避します。

Entity Framework Core は、同じ実行されている複数の並列操作をサポートしていません`DbContext`インスタンス。 これには、非同期クエリの並列実行と複数のスレッドからの明示的な同時使用の両方が含まれます。 そのため、常に`await`非同期呼び出しをすぐに、または個別`DbContext`並列実行される操作のインスタンス。

EF Core を使用しようとすると、検出した場合、`DbContext`わかりますを同時に、インスタンス、`InvalidOperationException`このようなメッセージ。 

> 2 番目の操作は、前の操作が完了する前に、このコンテキストで開始します。 これは通常発生別のスレッドで同じ DbContext のインスタンスを使用して、ただしインスタンス メンバーとは限りませんスレッド セーフであります。

同時アクセスを検出した場合、未定義の動作、アプリケーションのクラッシュおよびデータの破損の可能性します。

Inadvernetly 原因の同時アクセスは、同じことが一般的な誤りがある`DbContext`インスタンス。

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>同じ DbContext で他の操作を開始する前に非同期操作の完了を待機することを忘れる

非同期メソッドには、非ブロッキング方式で、データベースにアクセスする操作を開始する EF Core が有効にします。 場合は、呼び出し元が、これらのメソッドのいずれかの完了を待機せず、その他の操作を実行に進みますが、`DbContext`の状態、`DbContext`を指定できます (および多くの場合は) 破損しています。 

常に EF Core の非同期メソッドをすぐに待機してください。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>依存関係の挿入を使用して複数のスレッド間で暗黙的に DbContext インスタンスを共有

[ `AddDbContext` ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)拡張メソッドを登録`DbContext`型、[有効期間がスコープ](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)既定。 

これは、特定の時点で各クライアント要求を実行する 1 つのスレッドがあるため、各要求は、別の依存関係の挿入のスコープを取得するための同時アクセスの問題、ASP.NET Core アプリケーションで安全な (したがって独立した`DbContext`インスタンスの場合)。

ただし、明示的に複数のスレッドを並行で実行されるコードを確認する必要がありますを`DbContext`インスタンスは同時に accesed されてこと。

依存関係の挿入を使用して、これを行うかが対象とし、作成スコープとコンテキストを登録することによって (を使用して`IServiceScopeFactory`) スレッドごと、または登録することによって、`DbContext`一時的なものとして (のオーバー ロードを使用して`AddDbContext`を受け取ります`ServiceLifetime`パラメーター)。

## <a name="more-reading"></a>複数の読み取り

* 読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を使用して ASP.NET Core での詳細についてはします。
* 読み取り[依存関係の注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を DI を使用する方法の詳細を参照してください。
* 読み取り[テスト](testing/index.md)詳細についてはします。
