---
title: デザイン時 DbContext の作成-EF Core
description: Entity Framework Core を使用したデザイン時の DbContext の作成方法
author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: a9d7da2bb76d60ca63eb0dc189f924df125f0a7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062010"
---
# <a name="design-time-dbcontext-creation"></a>デザイン時 DbContext 作成

一部の EF Core ツールコマンド (たとえば、 [移行][1] コマンド) では、 `DbContext` アプリケーションのエンティティ型に関する詳細情報を収集し、データベーススキーマにマップする方法に基づいて、デザイン時に派生インスタンスを作成する必要があります。 ほとんどの場合、 `DbContext` 作成されるは実行時 [に構成][2]するのと同様の方法で構成することをお勧めします。

ツールでは、さまざまな方法でを作成し `DbContext` ます。

## <a name="from-application-services"></a>アプリケーションサービスから

スタートアッププロジェクトで [ASP.NET Core Web ホスト][3] または [.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。

ツールは、まずを呼び出し、を呼び出して、プロパティにアクセスすることで、サービスプロバイダーを取得しようとし `Program.CreateHostBuilder()` `Build()` `Services` ます。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。

`DbContext`自身とそのコンストラクター内の依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。 これは[、 `DbContext` のインスタンスを `DbContextOptions<TContext>` 引数として受け取り][5]、 [ `AddDbContext<TContext>` メソッド][6]を使用して、にコンストラクターを設定することによって簡単に実現できます。

## <a name="using-a-constructor-with-no-parameters"></a>パラメーターのないコンストラクターを使用する

DbContext をアプリケーションサービスプロバイダーから取得できない場合、ツールは `DbContext` プロジェクト内で派生型を検索します。 次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。 がメソッドを使用して構成されている場合、これは既定のコンストラクターになることがあり `DbContext` [`OnConfiguring`][7] ます。

## <a name="from-a-design-time-factory"></a>デザイン時のファクトリから

また、インターフェイスを実装することによって DbContext を作成する方法を指定することもでき `IDesignTimeDbContextFactory<TContext>` ます。このインターフェイスを実装するクラスが、派生プロジェクトまたはアプリケーションのスタートアッププロジェクトと同じプロジェクトに存在する場合 `DbContext` 、ツールは dbcontext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> EFCore 5.0 より前では、 `args` パラメーターは使用されていませんでした ( [この問題][8]を参照してください)。
> これは EFCore 5.0 で修正され、追加のデザイン時引数は、そのパラメーターを通じてアプリケーションに渡されます。

デザイン時のファクトリは、実行時とは異なる方法で DbContext をデザイン時に構成する必要がある場合、コンストラクターが DI に登録されていない場合、 `DbContext` di を使用していない場合、または何らかの理由で `BuildWebHost` ASP.NET Core アプリケーションのクラスにメソッドを使用しない場合に特に便利です `Main` 。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
