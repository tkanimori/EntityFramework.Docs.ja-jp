---
title: 以前のバージョンから EF Core 2 EF Core へのアップグレード
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 42e59b47f569ef6fcf72fc5bd5f94d3e9d807a24
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813568"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>以前のバージョンから EF Core 2.0 にアプリケーションをアップグレードする

既存の Api と動作を2.0 で大幅に改善する機会を得ました。 既存のアプリケーションコードの変更が必要になることはいくつかありますが、ほとんどのアプリケーションでは影響が少ないと考えられますが、ほとんどの場合、再コンパイルだけで、最新ではない Api を置き換えるための最小限のガイド変更が必要です。

既存のアプリケーションを EF Core 2.0 に更新する場合、次のことが必要になることがあります。

1. アプリケーションのターゲット .NET 実装を、.NET Standard 2.0 をサポートするものにアップグレードします。 詳細については、「[サポートされる .net の実装](../platforms/index.md)」を参照してください。

2. EF Core 2.0 と互換性のあるターゲットデータベースのプロバイダーを特定します。 EF Core 2.0 を参照してください。次の[2.0 データベースプロバイダーが必要](#ef-core-20-requires-a-20-database-provider)です。

3. すべての EF Core パッケージ (ランタイムおよびツール) を2.0 にアップグレードします。 詳細については、「 [EF Core のインストール](../get-started/install/index.md)」を参照してください。

4. このドキュメントの残りの部分で説明されている互換性に影響する変更を補うために、必要なコード変更を行います。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core に EF Core が含まれるようになりました

ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 ただし、以前のバージョンの ASP.NET Core を対象とするアプリケーションでは、EF Core 2.0 を使用するために ASP.NET Core 2.0 にアップグレードする必要があります。 ASP.NET Core アプリケーションを2.0 にアップグレードする方法の詳細については[、ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)を参照してください。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>ASP.NET Core でアプリケーションサービスを取得するための新しい方法

ASP.NET Core web アプリケーションで推奨されるパターンは、1. x で使用 EF Core デザイン時のロジックが破損しているように2.0 に更新されています。 以前はデザイン時に、EF Core はアプリケーションのサービス`Startup.ConfigureServices`プロバイダーにアクセスするために、を直接呼び出そうとします。 ASP.NET Core 2.0 では、構成は`Startup`クラスの外部で初期化されます。 EF Core を使用するアプリケーションは通常、構成から接続文字列`Startup`にアクセスするので、それ自体では不十分です。 ASP.NET Core 1.x アプリケーションをアップグレードすると、EF Core ツールの使用時に次のエラーが表示される場合があります。

> ' ApplicationContext ' にパラメーターなしのコンストラクターが見つかりませんでした。 パラメーターなしのコンストラクターを ' applicationcontext ' に追加するか、' applicationcontext ' と同じアセンブリに&lt;' idesigntimedbcontextfactory applicationcontext&gt;' の実装を追加してください

新しいデザイン時フックが ASP.NET Core 2.0 の既定のテンプレートに追加されました。 静的`Program.BuildWebHost`なメソッドを使用すると、デザイン時にアプリケーションのサービスプロバイダーにアクセスする EF Core できます。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、次のように`Program`クラスを更新する必要があります。

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

アプリケーションを2.0 に更新するときにこの新しいパターンを採用することを強くお勧めします。 Entity Framework Core の移行などの製品機能を使用するには、この新しいパターンが必要です。 もう1つの一般的な方法は、 [ *\<idesigntimedbcontextfactory tcontext >* を実装](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)することです。

## <a name="idbcontextfactory-renamed"></a>IDbContextFactory の名前変更

さまざまなアプリケーションパターンをサポートし、ユーザー `DbContext`がデザイン時の使用方法をより細かく制御できるようにするために、以前は`IDbContextFactory<TContext>`インターフェイスを提供してきました。 デザイン時に、EF Core ツールは、プロジェクト内のこのインターフェイスの実装を検出し、それを使用`DbContext`してオブジェクトを作成します。

このインターフェイスには非常に一般的な名前が付いているため、一部のユーザーが`DbContext`他の作成シナリオで再利用を試みることがあります。 EF ツールが設計時に実装を使用しようとしたときに、や`Update-Database` `dotnet ef database update`などのコマンドが失敗すると、ガードが解除されました。

このインターフェイスの強力なデザイン時セマンティクスを伝えるために、名前をに`IDesignTimeDbContextFactory<TContext>`変更しました。

2\.0 リリースでは、 `IDbContextFactory<TContext>`は引き続き存在しますが、不使用とマークされています。

## <a name="dbcontextfactoryoptions-removed"></a>削除された DbContextFactoryOptions

上記で説明した ASP.NET Core 2.0 の変更により、 `DbContextFactoryOptions`新しい`IDesignTimeDbContextFactory<TContext>`インターフェイスでは不要になったことがわかりました。 代わりに、次の代替手段を使用する必要があります。

| DbContextFactoryOptions | 代替                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>デザイン時の作業ディレクトリが変更されました

ASP.NET Core 2.0 の変更では、アプリケーションの実行時`dotnet ef`に Visual Studio によって使用される作業ディレクトリに合わせるために、で使用される作業ディレクトリも必要でした。 これは、SQLite ファイル名がプロジェクトディレクトリに対して相対的であり、のような出力ディレクトリではないという副作用の1つです。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 には2.0 データベースプロバイダーが必要です

EF Core 2.0 では、データベースプロバイダーの動作方法について、多くの簡素化と改善が行われています。 これは、1.0. x および 1.1. x プロバイダーが EF Core 2.0 で動作しないことを意味します。

SQL Server および SQLite プロバイダーは EF チームによって出荷され、2.0 バージョンは2.0 リリースの一部として提供されます。 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)のオープンソースのサードパーティプロバイダーは2.0 用に更新されています。 他のすべてのプロバイダーについては、プロバイダーの作成者にお問い合わせください。

## <a name="logging-and-diagnostics-events-have-changed"></a>ログ記録と診断イベントが変更されました

注: これらの変更は、ほとんどのアプリケーションコードに影響しないようにしてください。

[ILogger](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.ilogger)に送信されたメッセージのイベント id が2.0 で変更されました。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントでは、対応する`ILogger`メッセージと同じイベント ID 名が使用されるようになりました。 イベントペイロードは、 [EventData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)から派生したすべての公称型です。

イベント Id、ペイロードの種類、およびカテゴリは、 [Coreeventid](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)と[RelationalEventId](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)クラスに記載されています。

Id は、Microsoft の EntityFrameworkCore. Infrastructure 名前空間にも移動されています。

## <a name="ef-core-relational-metadata-api-changes"></a>リレーショナルメタデータ API の変更の EF Core

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、次のような 1.1. x コードです。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

次のようになります。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

のような`ForSqlServerToTable`メソッドを使用する代わりに、拡張メソッドを使用して、現在使用されているプロバイダーに基づいて条件付きコードを記述できるようになりました。 以下に例を示します。

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

この変更は、_すべて_のリレーショナルプロバイダーに対して定義されている api/メタデータにのみ適用されることに注意してください。 API とメタデータは、1つのプロバイダーのみに限定されていても変わりません。 たとえば、クラスター化インデックスは SQL server に固有のもの`ForSqlServerIsClustered`で`.SqlServer().IsClustered()`あるため、引き続き使用する必要があります。

## <a name="dont-take-control-of-the-ef-service-provider"></a>EF サービスプロバイダーを制御しない

内部実装には`IServiceProvider` 、内部 (依存関係挿入コンテナー) を使用 EF Core。 アプリケーションでは、特別な場合を除き、このプロバイダーを作成および管理する EF Core を許可する必要があります。 の呼び出しを削除すること`UseInternalServiceProvider`を強くお勧めします。 アプリケーションでを呼び出す`UseInternalServiceProvider`必要がある場合は、シナリオを処理する他の方法を調査できるように、問題を[提出](https://github.com/aspnet/EntityFramework/Issues)することを検討してください。

、 `AddEntityFramework`、 `AddEntityFrameworkSqlServer`などを呼び出すことは、が呼び出され`UseInternalServiceProvider`ない限り、アプリケーションコードでは必要ありません。 または`AddEntityFramework` `AddEntityFrameworkSqlServer`などへの既存の呼び出しを`AddDbContext`削除する場合でも、以前と同じ方法で使用する必要があります。

## <a name="in-memory-databases-must-be-named"></a>インメモリデータベースは、という名前にする必要があります。

名前のないグローバルなメモリ内データベースデータベースは削除されているため、すべてのインメモリデータベースに名前を付ける必要があります。 以下に例を示します。

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

これにより、"MyDatabase" という名前のデータベースが作成/使用されます。 同じ名前で再度を呼び出すと、同じメモリ内データベースが使用され、複数のコンテキストインスタンスで共有できるようになります。`UseInMemoryDatabase`

## <a name="read-only-api-changes"></a>読み取り専用 API の変更

`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave`、および`IsStoreGeneratedAlways`は廃止され、 [BeforeSaveBehavior](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)と[aftersavebehavior](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)に置き換えられました。 これらの動作は、(ストアによって生成されるプロパティだけでなく) 任意のプロパティに適用され、データベース行`BeforeSaveBehavior`に挿入するとき、または既存のデータベース行 (`AfterSaveBehavior`) を更新するときに、プロパティの値をどのように使用するかを決定します。

[Valuegenerated. OnAddOrUpdate](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (計算列など) としてマークされたプロパティは、既定では、プロパティに現在設定されている値を無視します。 これは、追跡対象のエンティティで値が設定または変更されたかどうかに関係なく、ストアによって生成される値が常に取得されることを意味します。 これは、別`Before\AfterSaveBehavior`のを設定することによって変更できます。

## <a name="new-clientsetnull-delete-behavior"></a>新しい ClientSetNull の削除動作

以前のリリースでは、 [deletebehavior. 制限](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.deletebehavior)は、終了したセマンティクスに一致`SetNull`したコンテキストによって追跡されるエンティティに対して動作がありました。 EF Core 2.0 では、オプション`ClientSetNull`のリレーションシップの既定値として新しい動作が導入されました。 この動作に`SetNull`は、EF Core を使用`Restrict`して作成されたデータベースの追跡対象エンティティおよび動作のセマンティクスがあります。 ここでは、追跡対象のエンティティとデータベースに対して最も期待される、または役に立つ動作を示します。 `DeleteBehavior.Restrict`は、オプションのリレーションシップが設定されている場合、追跡対象エンティティに対して使用されるようになりました

## <a name="provider-design-time-packages-removed"></a>プロバイダーのデザイン時に削除されたパッケージ

`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。 コンテンツはと`Microsoft.EntityFrameworkCore.Relational` `Microsoft.EntityFrameworkCore.Design`に統合されています。

これは、プロバイダーのデザイン時パッケージに反映されます。 これらのパッケージ`Microsoft.EntityFrameworkCore.Sqlite.Design`( `Microsoft.EntityFrameworkCore.SqlServer.Design`、など) は削除され、その内容はメインプロバイダーパッケージに統合されています。

EF Core 2.0 `Scaffold-DbContext`で`dotnet ef dbcontext scaffold`またはを有効にするには、1つのプロバイダーパッケージのみを参照する必要があります。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
