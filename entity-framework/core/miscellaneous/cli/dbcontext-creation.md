---
title: デザイン時 DbContext 作成 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 66fec7605b6ac2da0af1e801f8a1dca0789aea35
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993719"
---
<a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成
==============================
EF Core ツールのコマンドの一部 (たとえば、[移行][ 1]コマンド) 派生を必要と`DbContext`アプリケーションの詳細を収集するためにデザイン時に作成されるインスタンスエンティティ型とデータベース スキーマへの割り当て方法。 ほとんどの場合は望ましいを`DbContext`作成されたことがどのようになりますと同様の方法で構成されている[実行時に構成されている][2]。

さまざまな方法で作成しようとしているツール、 `DbContext`:

<a name="from-application-services"></a>アプリケーション サービスから
-------------------------
ASP.NET Core アプリをスタートアップ プロジェクトには、ツールは、アプリケーションのサービス プロバイダーから DbContext オブジェクトを取得しようとします。

呼び出すことによって、サービス プロバイダーを取得するツールがまず`Program.BuildWebHost()`にアクセスして、`IWebHost.Services`プロパティ。

> [!NOTE]
> 新しい ASP.NET Core 2.0 アプリケーションを作成するときに、このフックは既定で含まれています。 EF Core と ASP.NET Core の以前のバージョンでツールが起動しよう`Startup.ConfigureServices`不要になったアプリケーションのサービスのプロバイダーがこのパターンを取得するために正常に ASP.NET Core 2.0 アプリケーションで直接します。 ASP.NET Core 1.x アプリケーションを 2.0 にアップグレードする場合は、[変更、`Program`新しいパターンに従うクラス][3]します。

`DbContext`自体、およびそのコンス トラクターで依存関係は、アプリケーションのサービス プロバイダーのサービスとして登録されている必要があります。 ことによって簡単に達成この[コンス トラクター、`DbContext`のインスタンスを受け取る`DbContextOptions<TContext>`を引数として][ 4]を使用して、 [ `AddDbContext<TContext>` メソッド][5].

<a name="using-a-constructor-with-no-parameters"></a>パラメーターなしのコンス トラクターを使用します。
--------------------------------------
DbContext をアプリケーションのサービス プロバイダーから取得できない場合、ツールを探して、派生`DbContext`プロジェクト内の型。 パラメーターなしのコンス トラクターを使用してインスタンスを作成、再試行してください。 場合、既定のコンス トラクターができます、`DbContext`を使用して、構成、 [ `OnConfiguring` ] [ 6]メソッド。

<a name="from-a-design-time-factory"></a>デザイン時のファクトリから
--------------------------
見分けることができます、ツールを実装することで、DbContext を作成する方法、`IDesignTimeDbContextFactory<TContext>`インターフェイス: このインターフェイスを実装するクラスを派生と同じプロジェクトのいずれかで見つかった場合`DbContext`またはアプリケーションのスタートアップ プロジェクトで、ツールのバイパス他の方法の代わりに、DbContext と使用、デザイン時のファクトリを作成します。

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

デザイン時のファクトリは、場合、実行時によりも、デザイン時の異なる方法で DbContext を構成する必要がある場合に特に役立ちます、 `DbContext` DI をまったく使用しない場合に、追加のパラメーターは、DI に登録されていないコンス トラクターがまたはのいくつかの場合理由を行わない、`BuildWebHost`メソッドで、ASP.NET Core アプリケーションの`Main`クラス。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
