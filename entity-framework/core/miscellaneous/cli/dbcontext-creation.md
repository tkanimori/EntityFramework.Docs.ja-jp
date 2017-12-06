---
title: "デザイン時 DbContext 作成 - EF コア"
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 5fcd9e362d76127e7acadd9e552ef3ac90967a37
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成
==============================
コマンドには、デザイン時に作成 DbContext インスタンスが必要がある、EF ツールの一部は時間 (たとえば、移行のコマンドを実行している) 場合。 ツールを作成しようとするさまざまな方法があります。

<a name="from-application-services"></a>アプリケーション サービスから
-------------------------
スタートアップ プロジェクトが ASP.NET Core アプリケーションの場合は、ツールは、アプリケーションのサービス プロバイダーから DbContext オブジェクトを取得しようとします。 呼び出すことによって取得する`Program.BuildWebHost()`にアクセスして、`IWebHost.Services`プロパティです。 使用して、DbContext 登録`IServiceCollection.AddDbContext<TContext>()`見つかった、この方法で作成されたことができます。 このパターンが[ASP.NET Core 2.0 で導入されました][1]

<a name="using-the-default-constructor"></a>既定のコンス トラクターを使用します。
-----------------------------
DbContext をアプリケーション サービス プロバイダーから取得できない場合、ツールは、プロジェクト内、DbContext 型を探します。 既定のコンス トラクターを使用して作成しようとするとします。

<a name="from-a-design-time-factory"></a>デザイン時のファクトリから
--------------------------
見分けることができます、ツールを実装することによって、DbContext を作成する方法`IDesignTimeDbContextFactory`です。 このインターフェイスを実装するクラスがプロジェクト内で見つかった場合、ツールは DbContext を作成するその他の方法をバイパスします。
常に、これらは、デザイン時に、ファクトリを使用します。 ファクトリは、ランタイムではなくデザイン時の DbContext を異なる方法で構成する必要がある場合に特に便利です。

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

> [!NOTE]
> `args`パラメーターは現在使用されていません。 ある[問題][ 2]追跡ツールからのデザイン時の引数を指定する機能。

  [1]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [2]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
