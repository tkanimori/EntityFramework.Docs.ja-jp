---
title: 以前のバージョンから EF コア 2 - EF コアへのアップグレード
author: divega
ms.author: divega
ms.date: 8/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
ms.technology: entity-framework-core
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 30f4de794d42b1385145286e77c2e7c67987fea6
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678615"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>以前のバージョンから EF コア 2.0 へのアプリケーションのアップグレード

## <a name="procedures-common-to-all-applications"></a>すべてのアプリケーションに共通の手順

EF コア 2.0 に既存のアプリケーションの更新が必要です。

1. .NET 標準 2.0 をサポートしているアプリケーションのターゲットの .NET プラットフォームをアップグレードします。 参照してください[サポートされているプラットフォーム](../platforms/index.md)詳細についてはします。

2. EF コア 2.0 と互換性があるターゲット データベースのプロバイダーを識別します。 参照してください[EF コア 2.0 には、2.0 データベース プロバイダーが必要があります](#ef-core-20-requires-a-20-database-provider)以下です。

3. 2.0 へのすべての EF Core パッケージ (ランタイムおよびツール) をアップグレードします。 参照してください[EF Core のインストール](../get-started/install/index.md)詳細についてはします。

4. 重大な変更を補正するために必要なコード変更を加えます。 参照してください、[の重大な変更](#breaking-changes)詳細については、後述の「します。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

1. 具体的にを参照してください、[アプリケーションのサービス プロバイダーを初期化するための新しいパターン](#new-way-of-getting-application-services)以下で説明します。

> [!TIP]  
> この新しいパターンを強くお勧めし、Entity Framework の中核となる移行と同様に、製品の機能の動作するために必要な 2.0 アプリケーションの更新うちに導入します。 他の一般的な手段[実装*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)です。

2. ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 ただし、以前のバージョンの ASP.NET Core をターゲットとするアプリケーションでは、EF コア 2.0 を使用するために ASP.NET Core 2.0 にアップグレードする必要があります。 2.0 に ASP.NET Core アプリケーションのアップグレードに関する詳細を参照してください[ASP.NET Core のドキュメントにあるサブジェクトに](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)です。

## <a name="breaking-changes"></a>互換性に影響する変更点

大幅に改良、既存の Api 2.0 で動作する機会を移動しました。 既存のアプリケーション コードの変更が必要になる改良がいくつか行われて、アプリケーションの大部分のあることが影響を再コンパイルして古い Api を置換する最小限の画面の指示に従って変更だけを必要とするほとんどの場合、低なります。

### <a name="new-way-of-getting-application-services"></a>アプリケーション サービスを取得する新しい方法

1.x で EF コアが使用されるデザイン時のロジックを無効にした方法で 2.0 の ASP.NET Core web アプリケーションに対して、推奨パターンが更新されました。 以前のデザイン時に、EF コアを試行を呼び出す`Startup.ConfigureServices`アプリケーションのサービス プロバイダーにアクセスするために直接できます。 外部の ASP.NET Core 2.0 の構成が初期化されて、`Startup`クラスです。 通常 EF コアを使用してアプリケーションへのアクセス、接続文字列構成からため`Startup`自体では十分なできなくします。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、EF コア ツールを使用する場合、次のエラーを受信可能性があります。

> パラメーターなしコンス トラクターは、'ApplicationContext' で見つかりませんでした。 パラメーターなしのコンス トラクターを 'ApplicationContext' に追加するか、追加の実装 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 'ApplicationContext' と同じアセンブリ内

ASP.NET Core 2.0 の既定のテンプレートで、新しいデザイン時フックが追加されました。 静的な`Program.BuildWebHost`メソッドにより、EF のコアがデザイン時に、アプリケーションのサービス プロバイダーにアクセスします。 ASP.NET Core 1.x アプリケーションをアップグレードする場合は、更新する必要があります。`Program`クラスを、次のようになります。

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

### <a name="idbcontextfactory-renamed"></a>IDbContextFactory の名前を変更

さまざまなアプリケーション パターンをサポートし、方法をより細かく制御できるようにするために、`DbContext`使用は、デザイン時にある、以前は、指定、`IDbContextFactory<TContext>`インターフェイス。 デザイン時に、EF コア ツールはプロジェクトでこのインターフェイスの実装を見つけてを使用して作成`DbContext`オブジェクト。

このインターフェイスには、一部のユーザーに再を使用してその他の誤解する可能性が非常に一般的な名前が必要がある`DbContext`のシナリオを作成します。 デザイン時にその実装を使用する EF ツールが、試みられたときにガード オフ キャッチおよびのようなコマンドの原因となった`Update-Database`または`dotnet ef database update`が失敗します。

このインターフェイスの強力なデザイン時のセマンティクスを通信するために名前を変更しましたに`IDesignTimeDbContextFactory<TContext>`です。

2.0 リリース、`IDbContextFactory<TContext>`引き続き存在しますが、不使用とマークされます。

### <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions removed

変更のため、ASP.NET Core 2.0 上で説明した、ことを確認しました`DbContextFactoryOptions`が不要になった新しい`IDesignTimeDbContextFactory<TContext>`インターフェイスです。 ここでは、代替の代わりに使用する必要があります。

| DbContextFactoryOptions | 代替                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

### <a name="design-time-working-directory-changed"></a>デザイン時の作業ディレクトリが変更されました

使用される作業ディレクトリにも必要な ASP.NET Core 2.0 変更`dotnet ef`アプリケーションを実行するときに、Visual Studio で使用される作業ディレクトリに合うようにします。 この監視可能な副作用 1 つは、ファイル名は今すぐに対する相対パス、プロジェクト ディレクトリと出力ディレクトリではなく、使用するようにその SQLite です。

### <a name="ef-core-20-requires-a-20-database-provider"></a>EF コア 2.0 2.0 データベース プロバイダーが必要です。

EF コア 2.0 は、多くの簡素化と機能強化データベース プロバイダーの方法で作業を行いました。 これは 1.0.x および 1.1.x プロバイダーは、EF コア 2.0 では動作しないことを意味します。

EF チームによって、SQL Server と SQLite プロバイダーが付属してでき 2.0 バージョンは 2.0 の一部としてリリースされます。 オープン ソース サード パーティ プロバイダー [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 2.0 の更新中です。 その他のすべてのプロバイダーでは、プロバイダーの作成者に問い合わせてください。

### <a name="logging-and-diagnostics-events-have-changed"></a>ログ記録と診断イベントが変更されました

注: これらの変更では、ほとんどのアプリケーション コードを影響はありません。

イベント Id に送信されたメッセージについて、 [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)が 2.0 に変更します。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントは、対応するとして同じイベント ID 名を使用するようになりました`ILogger`メッセージ。 イベント ペイロードは、すべての標準型から派生した[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)です。

イベント Id、ペイロードの種類、およびカテゴリは、『、 [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)と[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)クラスです。

Id は、新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間にも Microsoft.EntityFrameworkCore.Infraestructure から移動されました。

### <a name="ef-core-relational-metadata-api-changes"></a>EF コア リレーショナル メタデータ API の変更

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、1.1.x コード次のようにします。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

次のようになりました記述する必要があります。

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

などのメソッドを使用する代わりに`ForSqlServerToTable`、拡張メソッドは現在使用中の現在のプロバイダーに基づく条件付きのコードを記述できます。 例:

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

この変更は、定義されている Api/メタデータにのみ適用する注_すべて_リレーショナル プロバイダー。 API とメタデータは、1 つのプロバイダーのみに固有であるときに、同じです。 たとえば、クラスター化インデックスは SQL Sever、特定ように`ForSqlServerIsClustered`と`.SqlServer().IsClustered()`まだを使用する必要があります。

### <a name="dont-take-control-of-the-ef-service-provider"></a>EF サービス プロバイダーを制御することがありません。

EF コアを使用して、内部`IServiceProvider`(依存関係の注入コンテナーなど) ため、内部の実装です。 アプリケーションでは、EF のコアを作成し、特殊なケースで以外は、このプロバイダーを管理するを許可する必要があります。 呼び出しの削除を検討してください`UseInternalServiceProvider`です。 アプリケーションを呼び出す必要がある場合`UseInternalServiceProvider`、しを検討してください[問題をファイリング](https://github.com/aspnet/EntityFramework/Issues)シナリオを処理するには、その他の方法で調査を行えるようにします。

呼び出す`AddEntityFramework`、 `AddEntityFrameworkSqlServer`、しない限り、アプリケーション コードによってなどが必要ありません`UseInternalServiceProvider`とも呼ばれます。 呼び出しの既存の削除`AddEntityFramework`または`AddEntityFrameworkSqlServer`など`AddDbContext`必要がありますも使用する同じ方法で以前と同様です。

### <a name="in-memory-databases-must-be-named"></a>インメモリ データベースを指定する必要があります。

名前のないメモリ内のグローバル データベースが削除され、代わりにメモリ内のすべてのデータベースを名前必要があります。 例:

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

これを作成/は"MyDatabase"という名前のデータベースが使用します。 場合`UseInMemoryDatabase`が再度呼び出され、同じ名前を持つ、複数のコンテキストのインスタンスで共有することができるメモリ内の同じデータベースが使用されます。

### <a name="read-only-api-changes"></a>読み取り専用の API の変更

`IsReadOnlyBeforeSave`、 `IsReadOnlyAferSave`、および`IsStoreGeneratedAlways`廃止され、で置き換えられました[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)と[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)です。 これらの動作は、任意のプロパティ (ストア生成のプロパティだけでなく) に適用され、データベースの行に挿入するときに、プロパティの値を使用する方法を決定 (`BeforeSaveBehavior`) ときに、行をデータベースを既存の更新または (`AfterSaveBehavior`)。

としてマークされているプロパティ[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (例: 計算列) が既定では無視してプロパティに設定されている任意の値。 これは、任意の値が設定または追跡対象エンティティで変更されたかどうかに関係なくストア生成の値が常に取得することを意味します。 これは、別の設定で変更できます`Before\AfterSaveBehavior`です。

### <a name="new-clientsetnull-delete-behavior"></a>新しい ClientSetNull 削除の動作

以前のリリースで[DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)がエンティティの動作がコンテキストによって追跡以上の一致した閉じたこと`SetNull`セマンティクスです。 EF コア 2.0 で新しい`ClientSetNull`動作が省略可能なリレーションシップの既定値として導入されました。 この動作は、`SetNull`追跡対象のエンティティのセマンティクスと`Restrict`EF コアを使用して作成されたデータベースの動作です。 経験では、これらは、追跡対象のエンティティとデータベースの最も期待/役に立つの動作です。 `DeleteBehavior.Restrict` 省略可能なリレーションシップを設定すると、追跡対象のエンティティのようになりました受け入れられます。

### <a name="provider-design-time-packages-removed"></a>プロバイダーのデザイン時パッケージの削除

`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。 ファイルの内容を統合していた`Microsoft.EntityFrameworkCore.Relational`と`Microsoft.EntityFrameworkCore.Design`です。

プロバイダーのデザイン時パッケージに反映されます。 それらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`など) が削除し、その内容がメイン プロバイダーのパッケージに統合します。

有効にする`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`EF コア 2.0 でのみ参照する必要が 1 つのプロバイダーのパッケージ。

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
