---
title: "DbContext - EF Core の構成"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 96abf3b94be3e1d19f833644f1c2f6f13fe0e730
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="configuring-a-dbcontext"></a>DbContext を構成します。

この記事の内容を構成するためのパターンを示しています、`DbContext`で`DbContextOptions`です。 オプションは、データ ストアの構成を選択して、主に使用されます。

## <a name="configuring-dbcontextoptions"></a>DbContextOptions を構成します。

`DbContext`インスタンスがあります`DbContextOptions`を実行するためにします。 これは、オーバーライドすることによって構成できます`OnConfiguring`、またはコンス トラクターの引数を使用して外部的に提供します。

両方を使用すると場合、`OnConfiguring`は付加的なものは、指定されたオプションで実行されるコンス トラクター引数を指定したオプションを上書きします。

### <a name="constructor-argument"></a>コンス トラクターの引数

コンス トラクターを持つコンテキスト コード

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
> DbContext の基底コンス トラクターでは、非ジェネリック バージョンも受け入れられる`DbContextOptions`です。 非ジェネリック バージョンを使用して複数のコンテキストの型を持つアプリケーションには推奨されません。

コンス トラクターの引数から初期化するためにアプリケーション コード

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

> [!WARNING]  
> `OnConfiguring`最後に発生したし、DI またはコンス トラクターから取得したオプションを上書きすることができます。 この方法では、(ない場合、完全なデータベースが対象) のテストに適していません。

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

アプリケーション コードを使用して初期化`OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

## <a name="using-dbcontext-with-dependency-injection"></a>依存関係の挿入で DbContext の使用

使用して EF サポート`DbContext`依存性の注入コンテナーにします。 使用して、DbContext 型をサービス コンテナーに追加することができます`AddDbContext<TContext>`です。

`AddDbContext`両方、DbContext 型と、 `TContext`、および`DbContextOptions<TContext>`サービス コンテナーからの挿入用に使用できます。

参照してください[読み取り多く](#more-reading)の下の依存関係の挿入に関する情報。

依存関係の挿入に dbcontext を追加します。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け入れる、DbContext 型に`DbContextOptions`です。

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
public MyController(BloggingContext context)
```

アプリケーション コードが (サービス プロバイダーを直接使用する、一般的な方法で):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="using-idesigntimedbcontextfactorytcontext"></a>使用します。`IDesignTimeDbContextFactory<TContext>`

上記のオプションを代わりにの実装を指定することも`IDesignTimeDbContextFactory<TContext>`します。 EF ツールは、このファクトリを使用して、DbContext のインスタンスを作成することができます。 移行など特定のデザイン時のエクスペリエンスを有効にするために必要な場合があります。

パブリックの既定のコンス トラクターを持たないコンテキスト型のデザイン時のサービスを有効にするには、このインターフェイスを実装します。 デザイン時のサービスでは、派生のコンテキストと同じアセンブリ内にあるこのインターフェイスの実装を自動的に検出されます。

例:

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

## <a name="more-reading"></a>複数の読み取り

* 読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を ASP.NET Core の使用方法に関する詳細。
* 読み取り[依存性の注入](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html)を DI の使用に関する詳細を参照してください。
* 読み取り[テスト](testing/index.md)詳細についてはします。
