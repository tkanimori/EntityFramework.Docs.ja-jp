---
title: デザイン時 DbContext の作成-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f83d4b16227d114a1cac1514667484a908fea4ac
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197578"
---
<a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成
==============================
一部の EF Core ツールコマンド (たとえば、[移行][1]コマンド) では、アプリケーションのエンティティ`DbContext`型に関する詳細情報を収集し、データベーススキーマにマップする方法に基づいて、デザイン時に派生インスタンスを作成する必要があります。 ほとんどの場合、作成されるは実行`DbContext`時[に構成][2]するのと同様の方法で構成することをお勧めします。

ツールでは、さまざまな方法で`DbContext`を作成します。

<a name="from-application-services"></a>アプリケーションサービスから
-------------------------
スタートアッププロジェクトで[ASP.NET Core Web ホスト][3]または[.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。

ツールは、まず`Program.CreateHostBuilder()`を呼び出し、を呼び出し`Build()`て、 `Services`プロパティにアクセスすることで、サービスプロバイダーを取得しようとします。

``` csharp
public class Program
{
    public static void Main(string[] args)
        => CreateHostBuilder(args).Build().Run();

    // EF Core uses this method at design time to access the DbContext
    public static IHostBuilder CreateHostBuilder(string[] args)
        => Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(
                webBuilder => webBuilder.UseStartup<Startup>());
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
        => services.AddDbContext<ApplicationDbContext>();
}

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

> [!NOTE]
> 新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。

自身`DbContext`とそのコンストラクター内の依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。 これは[、の`DbContext` `DbContextOptions<TContext>`インスタンスを引数][5]と[ `AddDbContext<TContext>` ][6]して受け取り、メソッドを使用して、にコンストラクターを設定することによって簡単に実現できます。

<a name="using-a-constructor-with-no-parameters"></a>パラメーターのないコンストラクターを使用する
--------------------------------------
Dbcontext をアプリケーションサービスプロバイダーから取得できない場合、ツールはプロジェクト内で派生`DbContext`型を検索します。 次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。 `DbContext`が[メソッド`OnConfiguring`][7]を使用して構成されている場合、これは既定のコンストラクターになることがあります。

<a name="from-a-design-time-factory"></a>デザイン時のファクトリから
--------------------------
また、 `IDesignTimeDbContextFactory<TContext>`インターフェイスを実装することで、dbcontext を作成する方法をツールに指示することもできます。このインターフェイスを実装しているクラスが、派生`DbContext`プロジェクトまたはアプリケーションのスタートアッププロジェクトと同じプロジェクトに存在する場合、これらのツールは dbcontext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
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

> [!NOTE]
> パラメーター `args`は現在使用されていません。 ツールからデザイン時引数を指定する機能の追跡には[問題][8]があります。

デザイン時のファクトリは、実行時とは異なるデザイン時に dbcontext を別の方法で構成する必要がある場合、 `DbContext`コンストラクターが di に登録されていない場合、di を使用していない場合、または理由は、ASP.NET Core アプリケーションの`BuildWebHost` `Main`クラスにメソッドを使用しないことです。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
