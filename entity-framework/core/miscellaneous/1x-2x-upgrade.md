---
title: 以前のバージョンから EF Core 2 - EF Core へのアップグレード
author: divega
ms.author: divega
ms.date: 8/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
ms.technology: entity-framework-core
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: dca9a3fb9e514b6eb22281a0f0140539681efb71
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949257"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>以前のバージョンから EF Core 2.0 へのアプリケーションのアップグレード

## <a name="procedures-common-to-all-applications"></a>すべてのアプリケーションに共通の手順

既存のアプリケーションを EF Core 2.0 を更新する必要があります。

1. .NET Standard 2.0 をサポートしているアプリケーションのターゲットの .NET プラットフォームをアップグレードします。 参照してください[サポートされているプラットフォーム](../platforms/index.md)の詳細。

2. EF Core 2.0 と互換性がターゲット データベースのプロバイダーを識別します。 参照してください[EF Core 2.0 には、2.0 データベース プロバイダーが必要があります](#ef-core-20-requires-a-20-database-provider)以下。

3. すべての EF Core パッケージ (ランタイムおよびツール) を 2.0 にアップグレードします。 参照してください[EF Core のインストール](../get-started/install/index.md)の詳細。

4. 重大な変更を補正するために必要なコード変更を加えます。 参照してください、[重大な変更](#breaking-changes)詳細については後述します。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

1. 具体的にを参照してください、[アプリケーションのサービス プロバイダーを初期化するための新しいパターン](#new-way-of-getting-application-services)以下で説明します。

> [!TIP]  
> 2.0 アプリケーションの更新は強く推奨され、動作する Entity Framework Core Migrations のような製品機能のために必要がときにこの新しいパターンの導入します。 その他の一般的な方法が[実装*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)します。

2. ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 ただし、以前のバージョンの ASP.NET Core を対象とするアプリケーションは、EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。 ASP.NET Core アプリケーションを 2.0 にアップグレードする方法の詳細を参照してください[サブジェクトで ASP.NET Core ドキュメント](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)します。

## <a name="breaking-changes"></a>互換性に影響する変更点

既存の Api と 2.0 の動作を大幅に絞り込むことを思い出させています。 既存のアプリケーション コードを変更することが必要になるいくつかの機能強化への影響が再コンパイルおよび廃止された Api を置換する最小限のガイド付きの変更だけを必要とするほとんどの場合は低くなりますが、大半のアプリケーションと考えています。

### <a name="new-way-of-getting-application-services"></a>アプリケーション サービスを取得する新しい方法

EF Core が 1.x で使用されるデザイン時ロジックを無効にした方法で 2.0 の ASP.NET Core web アプリケーションの推奨されるパターンが更新されました。 以前のデザイン時に、EF Core は呼び出しを試みる`Startup.ConfigureServices`アプリケーションのサービス プロバイダーにアクセスするために直接します。 外部で ASP.NET Core 2.0 では、構成が初期化されて、`Startup`クラス。 通常、EF Core を使用してアプリケーションへのアクセス、接続文字列構成からため`Startup`自体では十分なできなくします。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、EF Core ツールを使用する場合、次のエラーを受信可能性があります。

> 'ApplicationContext' で、パラメーターなしのコンス トラクターが見つかりませんでした。 パラメーターなしのコンス トラクターを 'ApplicationContext' に追加するか、追加の実装 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 'ApplicationContext' と同じアセンブリ内

ASP.NET Core 2.0 の既定のテンプレートで、新しいデザイン時フックが追加されました。 静的な`Program.BuildWebHost`メソッドは、デザイン時に、アプリケーションのサービス プロバイダーへのアクセスに EF Core を使用できます。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、更新する必要があります。`Program`クラスを、次のようになります。

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

### <a name="idbcontextfactory-renamed"></a>IDbContextFactory 名前変更

多様なアプリケーション パターンをサポートし、ユーザーを細かく制御できるようにするために、`DbContext`使用は、デザイン時があるまで、提供される、`IDbContextFactory<TContext>`インターフェイス。 デザイン時に、EF Core ツールは、プロジェクトでは、このインターフェイスの実装が検出して使用を作成する`DbContext`オブジェクト。

このインターフェイスが非常に一般的な名前を試す再を使用してその他のいくつかのユーザーを困惑させる`DbContext`のシナリオを作成します。 EF ツールは、デザイン時にその実装を使用する試みられたときに不意をつかれた状態されが発生したようにコマンドが`Update-Database`または`dotnet ef database update`失敗します。

このインターフェイスの強力なデザイン時のセマンティクスを伝達するために変更しましたに`IDesignTimeDbContextFactory<TContext>`します。

2.0 リリース、`IDbContextFactory<TContext>`も存在しますが、不使用とマークされます。

### <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions の削除

変更のため、ASP.NET Core 2.0 上で説明した、ことがわかりました`DbContextFactoryOptions`が不要になった新しい`IDesignTimeDbContextFactory<TContext>`インターフェイス。 ここでは、代替の代わりに使用する必要があります。

| DbContextFactoryOptions | 代替                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

### <a name="design-time-working-directory-changed"></a>デザイン時の作業ディレクトリが変更されました

使用される作業ディレクトリにも必要な ASP.NET Core 2.0 変更`dotnet ef`に合うよう、アプリケーションを実行するときに、Visual Studio で使用される作業ディレクトリにします。 この監視可能な副作用 1 つは、ファイル名はプロジェクト ディレクトリと出力ディレクトリではなく今すぐするために使用するようにその SQLite です。

### <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 には、2.0 データベース プロバイダーが必要です。

EF Core 2.0 には、多くの簡略化を行い、データベース プロバイダーの方法で機能強化を行いました。 これは 1.0.x および 1.1.x プロバイダーは EF Core 2.0 で動作しないことを意味します。

EF チームに、SQL Server と SQLite プロバイダーが付属して、2.0 バージョンが利用できる、2.0 の一部としてリリースします。 サード パーティ製のオープン ソースのプロバイダー [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 2.0 に対して更新されています。 その他のすべてのプロバイダーでは、プロバイダーの作成者に問い合わせてください。

### <a name="logging-and-diagnostics-events-have-changed"></a>ログ記録と診断のイベントが変更されました。

注: これらの変更ではほとんどのアプリケーション コードは影響しません。

イベント Id に送信されるメッセージについて、 [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)が 2.0 で変更されました。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントは、対応する同じイベント ID の名前を使用するようになりました`ILogger`メッセージ。 イベント ペイロードがすべての標準型から派生した[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)します。

イベント Id、ペイロードの種類、およびカテゴリに記載されている、 [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)と[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)クラス。

Id は、新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間にも Microsoft.EntityFrameworkCore.Infraestructure から移動されました。

### <a name="ef-core-relational-metadata-api-changes"></a>EF Core のリレーショナル メタデータ API の変更

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、1.1.x ようなコード:

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

このようなようになりました記述する必要があります。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

などのメソッドを使用する代わりに`ForSqlServerToTable`、拡張メソッドは使用中の現在のプロバイダーに基づく条件付きのコードを記述できるようになりました。 例えば:

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

この変更は、定義されている Api/メタデータにのみ適用されます_すべて_リレーショナル プロバイダー。 API とメタデータは、1 つのプロバイダーのみに固有である場合に、同じままです。 たとえば、クラスター化インデックスは SQL Sever に固有ように`ForSqlServerIsClustered`と`.SqlServer().IsClustered()`引き続き使用する必要があります。

### <a name="dont-take-control-of-the-ef-service-provider"></a>EF のサービス プロバイダーの管理を使用しません。

EF Core を使用して内部`IServiceProvider`(依存関係注入コンテナー) の内部実装します。 アプリケーションでは、EF Core を作成し、このプロバイダーは、特殊なケースで以外の管理を許可する必要があります。 呼び出しの削除を厳密に検討`UseInternalServiceProvider`します。 アプリケーションを呼び出す必要がある場合`UseInternalServiceProvider`、しを検討してください[問題をファイリング](https://github.com/aspnet/EntityFramework/Issues)シナリオを処理する他の方法で調査を行えるようにします。

呼び出す`AddEntityFramework`、 `AddEntityFrameworkSqlServer`、アプリケーション コードでは、しない限りなどは必要ありません`UseInternalServiceProvider`ともいいます。 既存の呼び出しをすべて削除`AddEntityFramework`または`AddEntityFrameworkSqlServer`など`AddDbContext`と同様に、以前とする必要があります使用もします。

### <a name="in-memory-databases-must-be-named"></a>メモリ内データベースを指定する必要があります。

グローバルな名前のないメモリ内データベースが削除され、代わりにメモリ内のすべてのデータベースを名前必要があります。 例えば:

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

これを作成/使用"MyDatabase"という名前のデータベース。 場合`UseInMemoryDatabase`が再度呼び出される同じ名前の同じメモリ内データベースが使用され、複数のコンテキスト インスタンスで共有することができます。

### <a name="read-only-api-changes"></a>読み取り専用の API の変更

`IsReadOnlyBeforeSave`、 `IsReadOnlyAferSave`、および`IsStoreGeneratedAlways`廃止され、置き換え[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)と[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)します。 これらの動作 (ストア生成プロパティだけでなく) プロパティには適用し、データベースの行に挿入するときのプロパティの値の使用方法を判断 (`BeforeSaveBehavior`) ときに、行をデータベース、既存の更新または (`AfterSaveBehavior`)。

プロパティとしてマーク[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (たとえば、計算列)、既定では無視されます、プロパティに設定されている任意の値。 これは、任意の値が設定や追跡対象エンティティで変更されているかどうかに関係なくストア生成値が常に得られることを意味します。 これは、さまざまな設定で変更できます`Before\AfterSaveBehavior`します。

### <a name="new-clientsetnull-delete-behavior"></a>新しい ClientSetNull 削除の動作

以前のリリースで[DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)がエンティティの動作をコンテキストによって追跡の詳細は一致した閉じられたこと`SetNull`セマンティクスです。 EF Core 2.0 で新しい`ClientSetNull`省略可能なリレーションシップの既定値としての動作が導入されています。 この動作は、`SetNull`追跡対象のエンティティのセマンティクスと`Restrict`EF Core を使用して作成されたデータベースの動作。 経験から言うと、これらは、追跡対象のエンティティとデータベースの最も期待/役立つ動作です。 `DeleteBehavior.Restrict` 省略可能なリレーションシップを設定する場合、追跡対象エンティティのようになりました受け入れられます。

### <a name="provider-design-time-packages-removed"></a>プロバイダーのデザイン時パッケージを削除

`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。 ファイルの内容が統合された`Microsoft.EntityFrameworkCore.Relational`と`Microsoft.EntityFrameworkCore.Design`します。

プロバイダーのデザイン時パッケージに反映されます。 これらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`など) が削除されましたし、その内容は、メイン プロバイダーのパッケージに統合します。

有効にする`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`EF Core 2.0 でのみ参照する必要が 1 つのプロバイダー パッケージ。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
