---
title: 以前のバージョンから EF Core 2 EF Core へのアップグレード
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: b27c09fdb6210dd7c6aa0c8bc912a8bd183c16b9
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414231"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>以前のバージョンから EF Core 2.0 にアプリケーションをアップグレードする

既存の Api と動作を2.0 で大幅に改善する機会を得ました。 既存のアプリケーションコードの変更が必要になることはいくつかありますが、ほとんどのアプリケーションでは影響が少ないと考えられますが、ほとんどの場合、再コンパイルだけで、最新ではない Api を置き換えるための最小限のガイド変更が必要です。

既存のアプリケーションを EF Core 2.0 に更新する場合、次のことが必要になることがあります。

1. アプリケーションのターゲット .NET 実装を、.NET Standard 2.0 をサポートするものにアップグレードします。 詳細については、「[サポートされる .net の実装](xref:core/platforms/index)」を参照してください。

2. EF Core 2.0 と互換性のあるターゲットデータベースのプロバイダーを特定します。 EF Core 2.0 を参照してください。次の[2.0 データベースプロバイダーが必要](#ef-core-20-requires-a-20-database-provider)です。

3. すべての EF Core パッケージ (ランタイムおよびツール) を2.0 にアップグレードします。 詳細については、「 [EF Core のインストール](xref:core/get-started/install/index)」を参照してください。

4. このドキュメントの残りの部分で説明されている互換性に影響する変更を補うために、必要なコード変更を行います。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core に EF Core が含まれるようになりました

ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 ただし、以前のバージョンの ASP.NET Core を対象とするアプリケーションでは、EF Core 2.0 を使用するために ASP.NET Core 2.0 にアップグレードする必要があります。 ASP.NET Core アプリケーションを2.0 にアップグレードする方法の詳細については[、ASP.NET Core のドキュメント](/aspnet/core/migration/1x-to-2x/)を参照してください。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>ASP.NET Core でアプリケーションサービスを取得するための新しい方法

ASP.NET Core web アプリケーションで推奨されるパターンは、1. x で使用 EF Core デザイン時のロジックが破損しているように2.0 に更新されています。 以前のデザイン時には、アプリケーションのサービスプロバイダーにアクセスするために、EF Core は `Startup.ConfigureServices` を直接呼び出そうとします。 ASP.NET Core 2.0 では、構成は `Startup` クラスの外部で初期化されます。 EF Core を使用するアプリケーションは通常、構成から接続文字列にアクセスするため、`Startup` 自体では不十分です。 ASP.NET Core 1.x アプリケーションをアップグレードすると、EF Core ツールの使用時に次のエラーが表示される場合があります。

> ' ApplicationContext ' にパラメーターなしのコンストラクターが見つかりませんでした。 パラメーターなしのコンストラクターを ' ApplicationContext ' に追加するか、' IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' の実装を ' ApplicationContext ' と同じアセンブリに追加してください

新しいデザイン時フックが ASP.NET Core 2.0 の既定のテンプレートに追加されました。 静的 `Program.BuildWebHost` メソッドを使用すると、デザイン時に EF Core がアプリケーションのサービスプロバイダーにアクセスできるようになります。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、次のように `Program` クラスを更新する必要があります。

``` csharp
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

アプリケーションを2.0 に更新するときにこの新しいパターンを採用することを強くお勧めします。 Entity Framework Core の移行などの製品機能を使用するには、この新しいパターンが必要です。 もう1つの一般的な方法は、 [ *Idesigntimedbcontextfactory\<tcontext >* を実装](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)することです。

## <a name="idbcontextfactory-renamed"></a>IDbContextFactory の名前変更

さまざまなアプリケーションパターンをサポートし、デザイン時の `DbContext` の使用方法をユーザーがより細かく制御できるようにするために、以前は `IDbContextFactory<TContext>` インターフェイスを用意しました。 デザイン時に、EF Core ツールは、プロジェクト内のこのインターフェイスの実装を検出し、それを使用して `DbContext` オブジェクトを作成します。

このインターフェイスには非常に一般的な名前が付いています。一部のユーザーは、他の `DbContext`作成シナリオで再利用を試みることができます。 EF ツールが設計時に実装を使用しようとしたときに、`Update-Database` や `dotnet ef database update` などのコマンドが失敗する原因となったときに、ガードが検出されました。

このインターフェイスの強力なデザイン時セマンティクスを伝えるために、名前を `IDesignTimeDbContextFactory<TContext>`に変更しました。

2\.0 リリースでは、`IDbContextFactory<TContext>` はまだ存在していますが、不使用とマークされています。

## <a name="dbcontextfactoryoptions-removed"></a>削除された DbContextFactoryOptions

上記で説明した ASP.NET Core 2.0 の変更のため、新しい `IDesignTimeDbContextFactory<TContext>` インターフェイスで `DbContextFactoryOptions` が不要になったことがわかりました。 代わりに、次の代替手段を使用する必要があります。

| DbContextFactoryOptions | 代替手段                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>デザイン時の作業ディレクトリが変更されました

ASP.NET Core 2.0 の変更には、アプリケーションの実行時に Visual Studio で使用される作業ディレクトリに合わせるために `dotnet ef` で使用される作業ディレクトリも必要でした。 これは、SQLite ファイル名がプロジェクトディレクトリに対して相対的であり、のような出力ディレクトリではないという副作用の1つです。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 には2.0 データベースプロバイダーが必要です

EF Core 2.0 では、データベースプロバイダーの動作方法について、多くの簡素化と改善が行われています。 これは、1.0. x および 1.1. x プロバイダーが EF Core 2.0 で動作しないことを意味します。

SQL Server および SQLite プロバイダーは EF チームによって出荷され、2.0 バージョンは2.0 リリースの一部として提供されます。 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)のオープンソースのサードパーティプロバイダーは2.0 用に更新されています。 他のすべてのプロバイダーについては、プロバイダーの作成者にお問い合わせください。

## <a name="logging-and-diagnostics-events-have-changed"></a>ログ記録と診断イベントが変更されました

注: これらの変更は、ほとんどのアプリケーションコードに影響しないようにしてください。

[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)に送信されたメッセージのイベント id が2.0 で変更されました。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントでは、対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。 イベントペイロードは、 [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)から派生したすべての公称型です。

イベント Id、ペイロードの種類、およびカテゴリは、 [Coreeventid](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)と[RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)クラスに記載されています。

Id は、Microsoft の EntityFrameworkCore. Infrastructure 名前空間にも移動されています。

## <a name="ef-core-relational-metadata-api-changes"></a>リレーショナルメタデータ API の変更の EF Core

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 これにより、下位レベルのメタデータ Api を簡単に使用できるようになりました。これにより、_一般的なリレーショナルメタデータの概念_へのアクセスは、`.SqlServer`、`.Sqlite`などではなく `.Relational` を呼び出すことで常に行われます。たとえば、次のような 1.1. x コードです。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

次のようになります。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

`ForSqlServerToTable`のようなメソッドを使用する代わりに、現在使用されているプロバイダーに基づいて条件付きコードを記述するために拡張メソッドを使用できるようになりました。 次に例を示します。

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

この変更は、_すべて_のリレーショナルプロバイダーに対して定義されている api/メタデータにのみ適用されることに注意してください。 API とメタデータは、1つのプロバイダーのみに限定されていても変わりません。 たとえば、クラスター化インデックスは SQL server に固有のものであるため、`ForSqlServerIsClustered` と `.SqlServer().IsClustered()` を引き続き使用する必要があります。

## <a name="dont-take-control-of-the-ef-service-provider"></a>EF サービスプロバイダーを制御しない

内部実装には、内部 `IServiceProvider` (依存関係挿入コンテナー) EF Core 使用します。 アプリケーションでは、特別な場合を除き、このプロバイダーを作成および管理する EF Core を許可する必要があります。 `UseInternalServiceProvider`の呼び出しを削除することを強くお勧めします。 アプリケーションで `UseInternalServiceProvider`を呼び出す必要がある場合は、[問題](https://github.com/aspnet/EntityFramework/Issues)を報告して、シナリオに対処するための他の方法を調査することを検討してください。

`UseInternalServiceProvider` も呼び出されない限り、`AddEntityFramework`、`AddEntityFrameworkSqlServer`などを呼び出すことは、アプリケーションコードでは必要ありません。 `AddEntityFramework` または `AddEntityFrameworkSqlServer`に対する既存の呼び出しを削除します。 `AddDbContext` は、前と同じ方法で使用する必要があります。

## <a name="in-memory-databases-must-be-named"></a>インメモリデータベースは、という名前にする必要があります。

名前のないグローバルなメモリ内データベースデータベースは削除されているため、すべてのインメモリデータベースに名前を付ける必要があります。 次に例を示します。

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

これにより、"MyDatabase" という名前のデータベースが作成/使用されます。 同じ名前で `UseInMemoryDatabase` が再度呼び出されると、同じメモリ内データベースが使用され、複数のコンテキストインスタンスで共有できるようになります。

## <a name="read-only-api-changes"></a>読み取り専用 API の変更

`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`、および `IsStoreGeneratedAlways` は廃止され、 [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)と[aftersavebehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)に置き換えられました。 これらの動作は、(ストアによって生成されるプロパティだけでなく) 任意のプロパティに適用され、データベース行 (`BeforeSaveBehavior`) に挿入するとき、または既存のデータベース行 (`AfterSaveBehavior`) を更新するときに、プロパティの値をどのように使用するかを決定します。

[Valuegenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (計算列など) としてマークされたプロパティは、既定では、プロパティに現在設定されている値を無視します。 これは、追跡対象のエンティティで値が設定または変更されたかどうかに関係なく、ストアによって生成される値が常に取得されることを意味します。 これは、別の `Before\AfterSaveBehavior`を設定することによって変更できます。

## <a name="new-clientsetnull-delete-behavior"></a>新しい ClientSetNull の削除動作

以前のリリースでは、 [Deletebehavior. 制限](/dotnet/api/microsoft.entityframeworkcore.deletebehavior)は、終了したコンテキストによって追跡されたエンティティに対して、`SetNull` セマンティクスに一致しました。 EF Core 2.0 では、オプションのリレーションシップの既定値として新しい `ClientSetNull` 動作が導入されました。 この動作には、EF Core を使用して作成されたデータベースの追跡対象エンティティと `Restrict` 動作の `SetNull` セマンティクスがあります。 ここでは、追跡対象のエンティティとデータベースに対して最も期待される、または役に立つ動作を示します。 `DeleteBehavior.Restrict` は、オプションのリレーションシップが設定されている場合、追跡対象エンティティに対して使用されるようになりました

## <a name="provider-design-time-packages-removed"></a>プロバイダーのデザイン時に削除されたパッケージ

`Microsoft.EntityFrameworkCore.Relational.Design` パッケージが削除されました。 コンテンツは `Microsoft.EntityFrameworkCore.Relational` と `Microsoft.EntityFrameworkCore.Design`に統合されました。

これは、プロバイダーのデザイン時パッケージに反映されます。 これらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`など) が削除され、その内容がメインプロバイダーパッケージに統合されました。

EF Core 2.0 で `Scaffold-DbContext` または `dotnet ef dbcontext scaffold` を有効にするには、1つのプロバイダーパッケージのみを参照する必要があります。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
