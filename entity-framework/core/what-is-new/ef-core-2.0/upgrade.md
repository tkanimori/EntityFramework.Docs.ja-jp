---
title: 以前のバージョンから EF Core 2 へのアップグレード - EF Core
description: Entity Framework Core 2.0 へのアップグレードの手順と注意事項
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: c7c736629209da99f191ceb0d4000d19f40414b9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063440"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>以前のバージョンから EF Core 2.0 へのアプリケーションのアップグレード

2\.0 では既存の API と動作を大幅に改善する機会を得ました。 いくつかの改善点では既存のアプリケーション コードの変更を必要とする可能性があります。ほとんどのアプリケーションで、その影響は少ないと考えられ、ほとんどの場合は、古い API を置き換えるための再コンパイルと最小限のガイド付き変更が必要なだけです。

既存のアプリケーションを EF Core 2.0 に更新する場合は、次のことが必要になることがあります。

1. アプリケーションのターゲット .NET 実装を、.NET Standard 2.0 をサポートするものにアップグレードします。 詳細については、[サポートされている .NET 実装](xref:core/platforms/index)に関するページを参照してください。

2. EF Core 2.0 と互換性のあるターゲット データベース用のプロバイダーを特定します。 「[EF Core 2.0 には 2.0 データベース プロバイダーが必要](#ef-core-20-requires-a-20-database-provider)」を参照してください。

3. すべての EF Core パッケージ (ランタイムおよびツール) を 2.0 にアップグレードします。 詳細については、「[EF Core のインストール](xref:core/get-started/install/index)」を参照してください。

4. このドキュメントの残りの部分で説明されている破壊的変更を補正するために必要なコード変更を行います。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core に EF Core が含まれるようになりました

ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 ただし、前バージョンの ASP.NET Core をターゲットとするアプリケーションで EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。 ASP.NET Core アプリケーションを 2.0 にアップグレードする方法の詳細については、[この件に関する ASP.NET Core のドキュメント](/aspnet/core/migration/1x-to-2x/)を参照してください。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>ASP.NET Core でアプリケーション サービスを取得するための新しい方法

ASP.NET Core Web アプリケーションに推奨されるパターンは、2.0 では、1.x で使用されているデザイン時ロジック EF Core を破壊する方法で更新されています。 以前はデザイン時に、EF Core はアプリケーションのサービス プロバイダーにアクセスするために、`Startup.ConfigureServices` の直接呼び出しを試みていました。 ASP.NET Core 2.0 では、構成は `Startup` クラスの外部で初期化されます。 EF Core を使用するアプリケーションでは、通常、構成から接続文字列へのアクセスが行われるため、`Startup` だけではもはや十分ではありません。 ASP.NET Core 1.x アプリケーションをアップグレードすると、EF Core ツールの使用時に次のエラーが表示される場合があります。

> 'ApplicationContext' にパラメーターなしのコンストラクターが見つかりませんでした。 パラメーターなしのコンストラクターを ' ApplicationContext' に追加するか、'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' の実装を 'ApplicationContext' と同じアセンブリに追加してください

新しいデザイン時フックが ASP.NET Core 2.0 の既定のテンプレートに追加されました。 静的メソッド `Program.BuildWebHost` を使用すると、デザイン時に EF Core がアプリケーションのサービスプロバイダーにアクセスするのを可能にすることができます。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、次のように `Program` クラスを更新する必要があります。

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

アプリケーションを 2.0 に更新するとき、この新しいパターンを採用することを強くお勧めします。このことは、Entity Framework Core Migrations などの製品機能を動作させるために必要です。 もう 1 つの一般的な方法は、[*IDesignTimeDbContextFactory\<TContext> を実装する*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)ことです。

## <a name="idbcontextfactory-renamed"></a>IDbContextFactory の名前変更

さまざまなアプリケーション パターンをサポートし、デザイン時の `DbContext` の使用方法をユーザーがより細かく制御できるようにするために、以前に `IDbContextFactory<TContext>` インターフェイスを用意しました。 デザイン時に、EF Core ツールでは、プロジェクト内のこのインターフェイスの実装を検出し、それを使用して `DbContext` オブジェクトが作成されます。

このインターフェイスには非常に一般的な名前が付いているため、一部のユーザーが誤って他の `DbContext` 作成シナリオでそれを再利用しようとすることがありました。 デザイン時に EF ツールが彼らの実装を使用することを試み、それにより `Update-Database` や `dotnet ef database update` などのコマンドが失敗したとき、彼らは予期せぬ出来事に驚かされていました。

このインターフェイスの強力なデザイン時セマンティクスとやりとりするために、その名前を `IDesignTimeDbContextFactory<TContext>` に変更しました。

2\.0 リリースで、`IDbContextFactory<TContext>` はまだ存在していますが、非推奨とマークされています。

## <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions の削除

上記で説明した ASP.NET Core 2.0 の変更のため、新しい `IDesignTimeDbContextFactory<TContext>` インターフェイス上で `DbContextFactoryOptions` が不要になったことがわかりました。 代わりに、使用する必要がある代替手段を次に示します。

| DbContextFactoryOptions | 代替手段                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>デザイン時の作業ディレクトリの変更

ASP.NET Core 2.0 の変更では、`dotnet ef` が使用する作業ディレクトリを、アプリケーションの実行時に Visual Studio が使用する作業ディレクトリに合わせることも必要でした。 このことの観測可能な副作用の 1 つは、SQLite ファイル名が、以前のように出力ディレクトリに対してではなく、プロジェクト ディレクトリに対して相対的になったことです。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 には 2.0 データベース プロバイダーが必要

EF Core 2.0 では、データベース プロバイダーの動作方法について、多くの簡素化と改善が行われています。 これは 1.0.x および 1.1.x プロバイダーが EF Core 2.0 で動作しなくなることを意味します。

SQL Server および SQLite プロバイダーは EF チームによって出荷され、2.0 バージョンは 2.0 リリースの一部として提供されます。 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 用のオープンソースのサードパーティ プロバイダーは、2.0 に対して更新されています。 他のすべてのプロバイダーについては、プロバイダーの作成者にお問い合わせください。

## <a name="logging-and-diagnostics-events-have-changed"></a>ログおよび診断イベントの変更

注: これらの変更は、ほとんどのアプリケーション コードに影響ありません。

[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) に送信されるメッセージのイベント ID が 2.0 で変更されました。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) イベントでは、それに対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。 イベント ペイロードはすべて、[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata) から派生する標準型です。

イベント ID、ペイロードの種類、およびカテゴリは、[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) および [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) クラスに記載されています。

ID もまた、Microsoft.EntityFrameworkCore.Infrastructure から新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間に移動されています。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core リレーショナル メタデータ API の変更

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、次のような 1.1.x コードがあるとします。

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

次のように記述されるようになります。

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

`ForSqlServerToTable` のようなメソッドを使用する代わりに、拡張メソッドを使用できるようになり、現在使用しているプロバイダーに基づいて条件付きコードを記述できるようになりました。 次に例を示します。

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

この変更は_すべての_リレーショナル プロバイダーに対して定義されている API およびメタデータにのみ適用されることに注意してください。 API とメタデータは、1 つのプロバイダーのみに限定されている場合、変りありません。 たとえば、クラスター化インデックスは SQL Server に固有のものであるため、`ForSqlServerIsClustered` と `.SqlServer().IsClustered()` を引き続き使用する必要があります。

## <a name="dont-take-control-of-the-ef-service-provider"></a>EF サービス プロバイダーを制御しない

EF Core では、その内部実装に内部 `IServiceProvider` (依存関係挿入コンテナー) が使用されます。 アプリケーションでは、特別な場合を除き、EF Core によるこのプロバイダーの作成および管理を許可する必要があります。 `UseInternalServiceProvider` の呼び出しを削除することを強くお勧めします。 アプリケーションで `UseInternalServiceProvider` を呼び出す必要がある場合は、こちらでお客様のシナリオに対処するための他の方法を調査できるように、[問題の報告](https://github.com/dotnet/efcore/Issues)を検討してください。

アプリケーション コードでは、`UseInternalServiceProvider` の呼び出しが行われない限り、`AddEntityFramework` や `AddEntityFrameworkSqlServer` などの呼び出しを行う必要はありません。 `AddEntityFramework` や `AddEntityFrameworkSqlServer` などの既存の呼び出しは削除します。`AddDbContext` は、前と同じ方法で使用する必要があります。

## <a name="in-memory-databases-must-be-named"></a>インメモリ データベースには名前を付ける必要があります

名前のないグローバルなインメモリ データベースは削除されているので、代わりに、すべてのインメモリ データベースに名前を付ける必要があります。 次に例を示します。

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

これにより、"MyDatabase" という名前のデータベースが作成および使用されます。 同じ名前で `UseInMemoryDatabase` が再度呼び出されると、同じインメモリ データベースが使用されるので、それを複数のコンテキスト インスタンスで共有することができます。

## <a name="read-only-api-changes"></a>読み取り専用 API の変更

`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`、`IsStoreGeneratedAlways` は廃止され、[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) および [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior) に置き換えられています。 これらの動作は、(ストア生成プロパティだけでなく) 任意のプロパティに適用され、データベース行 (`BeforeSaveBehavior`) に挿入するとき、または既存のデータベース行 (`AfterSaveBehavior`) を更新するときにプロパティの値をどのように使用すべきかを決定します。

[ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) とマークされたプロパティ (たとえば、計算列の場合) では、プロパティに現在設定されている値が既定で無視されます。 これは、追跡対象のエンティティ上で値が設定または変更されているかどうかに関係なく、ストア生成の値が常に取得されることを意味します。 これを変更するには、異なる `Before\AfterSaveBehavior` を設定します。

## <a name="new-clientsetnull-delete-behavior"></a>新しい ClientSetNull の削除動作

以前のリリースで、[DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) の動作は、`SetNull` セマンティクスにより限定的に一致するコンテキストによって追跡されるエンティティに対するものでした。 EF Core 2.0 では、新しい `ClientSetNull` 動作が、オプションのリレーションシップの既定値として導入されました。 この動作には、追跡対象エンティティに対する `SetNull` セマンティクスと、EF Core を使用して作成されたデータベースに対する `Restrict` 動作があります。 経験上で、これらは追跡対象のエンティティとデータベースに対して最も期待される、または役に立つ動作です。 `DeleteBehavior.Restrict` は、オプションのリレーションシップに設定された場合、追跡対象エンティティに対して使用されるようになりました。

## <a name="provider-design-time-packages-removed"></a>プロバイダーのデザイン時パッケージの削除

`Microsoft.EntityFrameworkCore.Relational.Design` パッケージは削除されました。 そのコンテンツは `Microsoft.EntityFrameworkCore.Relational` と `Microsoft.EntityFrameworkCore.Design` に統合されました。

これは、プロバイダーのデザイン時パッケージに反映されます。 それらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design` や `Microsoft.EntityFrameworkCore.SqlServer.Design` など) は削除され、その内容はメイン プロバイダー パッケージに統合されました。

EF Core 2.0 で `Scaffold-DbContext` または `dotnet ef dbcontext scaffold` を有効にするには、1 つのプロバイダー パッケージを参照するだけで済みます。

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
