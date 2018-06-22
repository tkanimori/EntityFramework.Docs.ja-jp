---
title: DbContext - EF Core の構成
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 6980acd53b0a74055af7a1e04b476f4625c327c9
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2018
ms.locfileid: "29152391"
---
# <a name="configuring-a-dbcontext"></a>DbContext を構成します。

この記事の内容を構成するための基本的なパターンを示しています、`DbContext`を介して、`DbContextOptions`特定 EF コア プロバイダーと省略可能な動作を使用してデータベースに接続します。

## <a name="design-time-dbcontext-configuration"></a>デザイン時 DbContext 構成

などの EF コア デザイン時ツール[移行](xref:core/managing-schemas/migrations/index)を検出しての作業のインスタンスを作成する必要がある、`DbContext`型アプリケーションのエンティティ型とデータベース スキーマへのマップ方法に関する詳細情報を収集するためにします。 ツールを簡単に作成できる限り、このプロセスは自動可能、`DbContext`をそれが構成されます同様に実行時の構成方法とするようにします。

必要な構成情報を提供する任意のパターンの中に、`DbContext`ランタイムの使用を必要とするツールで作業ができ、`DbContext`デザイン時にのみ使用できますパターンの数に制限します。 これらについては説明でより詳しく、[コンテキストの作成にデザイン時](xref:core/miscellaneous/cli/dbcontext-creation)セクションです。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions を構成します。

`DbContext` インスタンスがあります`DbContextOptions`のすべての作業を実行します。 `DbContextOptions`インスタンスなどの構成情報を実行します。

- データベース プロバイダーを使用するのには、通常などのメソッドを呼び出すことによって選択`UseSqlServer`または `UseSqlite`
- 任意の必要な接続文字列またはデータベースのインスタンスの識別子通常に渡される引数として上記プロバイダーの選択メソッド
- 通常、プロバイダーの選択メソッドの呼び出しの内部チェーンも、任意のプロバイダー レベル オプションの動作セレクター
- 通常チェーン プロバイダー セレクター メソッドは前に、または後に [全般]、EF コア動作セレクター

次の例では、構成、`DbContextOptions`に使用するには、SQL Server プロバイダーの接続が含まれている、`connectionString`変数、プロバイダ レベルのコマンドのタイムアウトとで実行されるすべてのクエリを使用する EF コア動作セレクター、 `DbContext`[いいえ追跡](xref:core/querying/tracking#no-tracking-queries)既定では。

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> プロバイダー セレクター メソッドとその他の動作セレクター メソッド上で説明したは、拡張メソッドで`DbContextOptions`またはプロバイダーに固有のオプション クラス。 これらの拡張メソッドが、名前空間が存在する必要がありますにアクセスするために (通常`Microsoft.EntityFrameworkCore`) のスコープを設定して、プロジェクトに追加のパッケージの依存関係を含めます。

`DbContextOptions`を指定すると、`DbContext`オーバーライドすることで、`OnConfiguring`メソッドまたはコンス トラクターの引数を使用して外部的です。

両方を使用すると場合、`OnConfiguring`が最後に適用され、コンス トラクター引数を指定したオプションを上書きすることができます。

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
> DbContext の基底コンス トラクターでは、非ジェネリック バージョンも受け入れられる`DbContextOptions`が非ジェネリック バージョンを使用しては複数のコンテキストの型を持つアプリケーションにはお勧めしません。

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

コンテキストのコードを`OnConfiguring`:

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
> このアプローチに適していないテスト、テスト対象のデータベースの完全しない限り、します。

### <a name="using-dbcontext-with-dependency-injection"></a>依存関係の挿入で DbContext の使用

使用して EF コア サポート`DbContext`依存性の注入コンテナーにします。 使用して、DbContext 型をサービス コンテナーに追加することができます、`AddDbContext<TContext>`メソッドです。

`AddDbContext<TContext>` 両方、DbContext 型と、`TContext`と、対応する`DbContextOptions<TContext>`サービス コンテナーからの挿入用に使用できます。

参照してください[読み取り多く](#more-reading)下依存関係の挿入の詳細についてはします。

追加する、`Dbcontext`依存関係の挿入に。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け入れる、DbContext 型に`DbContextOptions<TContext>`です。

コンテキスト コードに示します。

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

アプリケーション コードの ASP.NET Core):

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

アプリケーション コードが (サービス プロバイダーを直接使用する、一般的な方法で):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="more-reading"></a>複数の読み取り

* 読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を ASP.NET Core の使用方法に関する詳細。
* 読み取り[依存性の注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を DI の使用に関する詳細を参照してください。
* 読み取り[テスト](testing/index.md)詳細についてはします。
