---
title: デザイン時 DbContext の作成-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f44f0648678af5a70e5171d69692bde1c1d5e0eb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414219"
---
# <a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成

一部の EF Core ツールコマンド (たとえば、[移行][1]コマンド) では、アプリケーションのエンティティ型に関する詳細情報を収集し、データベーススキーマにマップする方法について、派生 `DbContext` インスタンスをデザイン時に作成する必要があります。 ほとんどの場合、作成した `DbContext` は、[実行時に構成][2]するのと同様の方法で構成することをお勧めします。

ツールで `DbContext`を作成するには、さまざまな方法があります。

## <a name="from-application-services"></a>アプリケーションサービスから

スタートアッププロジェクトで[ASP.NET Core Web ホスト][3]または[.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。

ツールは、まず `Program.CreateHostBuilder()`を呼び出し、`Build()`を呼び出して、`Services` プロパティにアクセスすることで、サービスプロバイダーを取得しようとします。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。

`DbContext` 自体とそのコンストラクター内のすべての依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。 これは、`DbContextOptions<TContext>` のインスタンスを引数として受け取り、 [`AddDbContext<TContext>` メソッド][6]を使用して[、`DbContext` にコンストラクターを配置する][5]ことで簡単に実現できます。

## <a name="using-a-constructor-with-no-parameters"></a>パラメーターのないコンストラクターを使用する

DbContext をアプリケーションサービスプロバイダーから取得できない場合、ツールはプロジェクト内で派生 `DbContext` 型を検索します。 次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。 `DbContext` が[`OnConfiguring`][7]メソッドを使用して構成されている場合、これは既定のコンストラクターになることがあります。

## <a name="from-a-design-time-factory"></a>デザイン時のファクトリから

また、`IDesignTimeDbContextFactory<TContext>` インターフェイスを実装することで DbContext を作成する方法を指定することもできます。このインターフェイスを実装するクラスが、派生 `DbContext` と同じプロジェクトまたはアプリケーションのスタートアッププロジェクトに存在する場合、ツールは DbContext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> `args` パラメーターは現在使用されていません。 ツールからデザイン時引数を指定する機能の追跡には[問題][8]があります。

デザイン時のファクトリは、実行時とは異なるデザイン時に DbContext を構成する必要がある場合、`DbContext` コンストラクターが DI に登録されていない場合、di を使用していない場合、または何らかの理由で ASP.NET Core アプリケーションの `Main` クラスに `BuildWebHost` メソッドを使用しない場合に特に便利です。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
