---
title: デザイン時 DbContext 作成 - EF コア
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 8b38d300d31038bdf5cd877aa3c42b7f5f97eabc
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
<a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成
==============================
EF コア ツールのコマンドの一部 (たとえば、[移行][ 1]コマンド) 派生を必要と`DbContext`アプリケーションの詳細を収集するためにデザイン時に作成されるインスタンスエンティティ型とデータベースのスキーマにどのようにマッピングされます。 ほとんどの場合では望ましくを`DbContext`これにより作成された方法だと同じ方法で構成された[実行時に構成されている][2]です。

さまざまな方法で作成しようとする、ツール、 `DbContext`:

<a name="from-application-services"></a>アプリケーション サービスから
-------------------------
スタートアップ プロジェクトが ASP.NET Core アプリケーションの場合は、ツールは、アプリケーションのサービス プロバイダーから DbContext オブジェクトを取得しようとします。

このツールは、まずを呼び出すことによって、サービス プロバイダーを取得する`Program.BuildWebHost()`にアクセスして、`IWebHost.Services`プロパティです。

> [!NOTE]
> 新しい ASP.NET Core 2.0 アプリケーションを作成するときに、既定ではこのフックが含まれています。 ツールを EF Core および ASP.NET Core の以前のバージョンを実行してみます`Startup.ConfigureServices`不要になったアプリケーションのサービス プロバイダーが、このパターンを取得するために正常に動作コア 2.0 の ASP.NET アプリケーションで直接です。 2.0 の ASP.NET Core 1.x アプリケーションをアップグレードする場合は[変更、`Program`新しいパターンに従うクラス][3]です。

`DbContext`自体と、コンス トラクターであらゆる依存先が、アプリケーションのサービス プロバイダーでサービスとして登録する必要があります。 これを行う簡単に用意することによって[のコンス トラクター、`DbContext`のインスタンスを受け取る`DbContextOptions<TContext>`を引数として][ 4]を使用して、 [ `AddDbContext<TContext>` メソッド][5].

<a name="using-a-constructor-with-no-parameters"></a>パラメーターなしのコンス トラクターを使用します。
--------------------------------------
DbContext をアプリケーション サービス プロバイダーから取得できない場合、ツールを探して、派生`DbContext`プロジェクト内の型。 パラメーターなしのコンス トラクターを使用してインスタンスを作成してください。 既定のコンス トラクターになる可能性が、`DbContext`を使用して、構成、 [ `OnConfiguring` ] [ 6]メソッドです。

<a name="from-a-design-time-factory"></a>デザイン時のファクトリから
--------------------------
見分けることができます、ツールを実装することによって、DbContext を作成する方法、`IDesignTimeDbContextFactory<TContext>`インターフェイス: このインターフェイスを実装するクラスを派生したのと同じプロジェクトのいずれかで見つかった場合は`DbContext`またはアプリケーションのスタートアップ プロジェクトでは、ツールのバイパスDbContext および使用して、デザイン時のファクトリを作成する代わりに他の方法です。

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
> `args`パラメーターは現在使用されていません。 ある[問題][ 7]追跡ツールからのデザイン時の引数を指定する機能。

デザイン時のファクトリは、場合は、DbContext を異なる方法で、実行時ではなくデザイン時用に構成する必要がある場合に特に便利です、 `DbContext` DI をまったく使用しない場合に、追加のパラメーターは、DI に登録されていないコンス トラクターがかかるか、一部の場合理由をしないようにする、 `BuildWebHost` ASP.NET Core アプリケーションの内のメソッド  
`Main` クラス。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
