---
title: Entity Framework 6 プロバイダーモデル-EF6
description: Entity Framework 6 の Entity Framework 6 プロバイダーモデル
author: divega
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: 4fc45ba5fe916253be348182196be236729d685d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074017"
---
# <a name="the-entity-framework-6-provider-model"></a>Entity Framework 6 プロバイダーモデル

Entity Framework プロバイダーモデルでは、さまざまな種類のデータベースサーバーで Entity Framework を使用できます。 たとえば、1つのプロバイダーを接続して Microsoft SQL Server に対して EF を使用できるようにし、別のプロバイダーを接続して Microsoft SQL Server Compact のエディションに対して EF を使用できるようにすることができます。 EF6 のプロバイダーは Entity Framework、[ [プロバイダー](xref:ef6/fundamentals/providers/index) ] ページで確認できます。

EF がプロバイダーと対話してオープンソースライセンスで EF を解放できるようにする方法には、特定の変更が必要でした。 これらの変更には、プロバイダーを登録するための新しいメカニズムと共に、EF6 アセンブリに対して EF プロバイダーを再構築する必要があります。

## <a name="rebuilding"></a>再構築

EF6 では、以前は .NET Framework の一部であったコアコードは帯域外 (OOB) アセンブリとして出荷されるようになりました。 EF6 に対してアプリケーションをビルドする方法の詳細については、 [EF6 用のアプリケーションの更新](xref:ef6/what-is-new/upgrading-to-ef6) に関するページを参照してください。 プロバイダーは、次の手順を使用して再構築する必要もあります。

## <a name="provider-types-overview"></a>プロバイダーの種類の概要

EF プロバイダーは、実際には、これらのサービスが (基底クラスの) から拡張するか、(インターフェイスの) を実装する CLR 型によって定義される、プロバイダー固有のサービスのコレクションです。 これらのサービスのうち2つは基本的なものであり、EF がまったく機能するために必要です。 それ以外は省略可能であり、特定の機能が必要な場合にのみ実装する必要があります。また、対象となる特定のデータベースサーバーでこれらのサービスの既定の実装が機能しない場合は、実装する必要があります。

## <a name="fundamental-provider-types"></a>基本的なプロバイダーの種類

### <a name="dbproviderfactory"></a>DbProviderFactory

EF は、すべての下位レベルのデータベースアクセスを実行するために [DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) から派生した型を持つかどうかに依存します。 DbProviderFactory は実際には EF の一部ではありませんが、代わりに、EF、他の O/RMs、またはアプリケーションが直接使用して、プロバイダーに依存しない方法で接続、コマンド、パラメーター、およびその他の ADO.NET 抽象化のインスタンスを取得することができる ADO.NET プロバイダーのエントリポイントを提供する .NET Framework のクラスです。 DbProviderFactory の詳細については、 [ADO.NET の MSDN ドキュメント](https://msdn.microsoft.com/library/a6cd7c08.aspx)を参照してください。

### <a name="dbproviderservices"></a>DbProviderServices

EF は、ADO.NET プロバイダーによって既に提供されている機能の上に EF が必要とする追加機能を提供するために、DbProviderServices から派生した型を持つかどうかに依存します。 以前のバージョンの EF では、DbProviderServices クラスは .NET Framework の一部であり、名前空間にありました。 EF6 以降では、このクラスは EntityFramework.dll の一部になり、名前空間に含まれるようになりました。

DbProviderServices 実装の基本的な機能の詳細については、 [MSDN](https://msdn.microsoft.com/library/ee789835.aspx)を参照してください。 ただし、この情報を書き込む時点では、ほとんどの概念はまだ有効ですが、EF6 の場合は更新されません。 DbProviderServices の SQL Server および SQL Server Compact の実装は、 [オープンソースのコードベース](https://github.com/aspnet/EntityFramework6/) にもチェックインされ、他の実装に役立つ参照として使用できます。

以前のバージョンの EF では、使用する DbProviderServices 実装は ADO.NET プロバイダーから直接取得されました。 これは、DbProviderFactory を IServiceProvider にキャストし、GetService メソッドを呼び出すことによって行われました。 これにより、EF プロバイダーと DbProviderFactory が密に結合されています。 この結合によって、EF は .NET Framework から除外されるようになりました。したがって、EF6 では、この密結合が削除され、DbProviderServices の実装がアプリケーションの構成ファイルまたはコードベースの構成に直接登録されました。詳細については、 _DbProviderServices の登録_ に関するセクションを参照してください。

## <a name="additional-services"></a>その他のサービス

上で説明した基本的なサービスに加えて、EF によって使用される他の多くのサービスも、常に、またはプロバイダー固有のものです。 これらのサービスの既定のプロバイダー固有の実装は、DbProviderServices 実装によって提供できます。 アプリケーションでは、これらのサービスの実装をオーバーライドしたり、DbProviderServices 型が既定値を提供しない場合の実装を提供したりすることもできます。 詳細については、後述する「その _他のサービスの解決_ 」を参照してください。

プロバイダーが関心を持つ可能性のある追加のサービスの種類を以下に示します。 これらのサービスの種類の詳細については、API のドキュメントを参照してください。

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

これは、プロバイダーがデータベースに対してクエリやコマンドを実行するときに再試行などの動作を実装できるようにするオプションのサービスです。 実装が指定されていない場合、EF は単にコマンドを実行し、スローされた例外をすべて伝達します。 SQL Server は、このサービスを使用して再試行ポリシーを提供します。これは、SQL Azure などのクラウドベースのデータベースサーバーに対して実行する場合に特に便利です。

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

これは、プロバイダーがデータベース名だけを指定した場合に規約によって DbConnection オブジェクトを作成できるようにするオプションのサービスです。 このサービスは、DbProviderServices 実装によって解決される場合がありますが、EF 4.1 以降に存在しています。また、構成ファイルまたはコードで明示的に設定することもできます。 プロバイダーは、既定のプロバイダーとして登録されている場合にのみ、このサービスを解決できるようになります (以下 _の既定のプロバイダー_ を参照してください)。既定の接続ファクトリが別の場所に設定されていない場合は、

### <a name="dbspatialservices"></a>DbSpatialServices

これは、プロバイダーが geography および geometry 空間型のサポートを追加できるようにするオプションのサービスです。 アプリケーションで空間型の EF を使用するには、このサービスの実装を指定する必要があります。 DbSptialServices は2つの方法で要求されます。 最初に、キーとして DbProviderInfo オブジェクト (不変名とマニフェストトークンを含む) を使用して、プロバイダー固有の空間サービスを要求します。 2つ目は、キーなしで DbSpatialServices を要求できることです。 これは、スタンドアロンの DbGeography 型または Dbgeography 型を作成するときに使用される "グローバル空間プロバイダー" を解決するために使用されます。

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

これは、Code First によってデータベーススキーマを作成および変更する際に使用される SQL の生成に EF の移行を使用できるようにするオプションのサービスです。 移行をサポートするためには、の実装が必要です。 実装が指定されている場合は、データベース初期化子またはデータベースの Create メソッドを使用してデータベースを作成するときにも使用されます。

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection、string、History Contextfactory>

これは、プロバイダーが、 `__MigrationHistory` EF の移行で使用されるテーブルへの履歴コンテキストのマッピングを構成できるようにするオプションのサービスです。 履歴コンテキストは Code First DbContext であり、テーブルの名前や列マッピングの仕様などを変更するために通常の fluent API を使用して構成できます。 すべての既定のテーブルと列のマッピングがそのプロバイダーでサポートされている場合、すべてのプロバイダーに対して EF によって返されたこのサービスの既定の実装は、特定のデータベースサーバーで機能する可能性があります。 このような場合、プロバイダーはこのサービスの実装を提供する必要はありません。

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

これは、指定された DbConnection オブジェクトから正しい DbProviderFactory を取得するためのオプションのサービスです。 すべてのプロバイダーに対して EF によって返されたこのサービスの既定の実装は、すべてのプロバイダーに対して機能することを目的としています。 ただし、.NET 4 で実行されている場合、DbProviderFactory には、DbConnections によってパブリックにアクセスすることはできません。 そのため、EF はいくつかのヒューリスティックを使用して、登録されたプロバイダーを検索し、一致するものを検索します。 一部のプロバイダーでは、これらのヒューリスティックが失敗し、そのような状況ではプロバイダーが新しい実装を提供する必要があります。

## <a name="registering-dbproviderservices"></a>DbProviderServices を登録しています

使用する DbProviderServices 実装は、アプリケーションの構成ファイル (app.config または web.config) で、またはコードベースの構成を使用して登録できます。 どちらの場合も、登録ではプロバイダーの "不変名" がキーとして使用されます。 これにより、複数のプロバイダーを1つのアプリケーションで登録して使用することができます。 EF の登録に使用される不変名は、ADO.NET プロバイダーの登録と接続文字列に使用される不変名と同じです。 たとえば、SQL Server では、不変名 "system.string" が使用されます。

### <a name="config-file-registration"></a>構成ファイルの登録

使用する DbProviderServices 型は、アプリケーションの構成ファイルの entityFramework セクションのプロバイダーの一覧にプロバイダー要素として登録されます。 次に例を示します。

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_型_の文字列は、使用する DbProviderServices 実装のアセンブリ修飾型名である必要があります。

### <a name="code-based-registration"></a>コード ベースの登録

EF6 プロバイダー以降では、コードを使用して登録することもできます。 これにより、アプリケーションの構成ファイルを変更することなく、EF プロバイダーを使用できるようになります。 コードベースの構成を使用するには、 [コードベースの構成のドキュメント](https://msdn.com/data/jj680699)で説明されているように、アプリケーションで dbconfiguration クラスを作成する必要があります。 DbConfiguration クラスのコンストラクターは、SetProviderServices を呼び出して EF プロバイダーを登録する必要があります。 次に例を示します。

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>その他のサービスの解決

「プロバイダーの種類の _概要_ 」セクションで前述したように、DbProviderServices クラスを使用して追加のサービスを解決することもできます。 DbProviderServices が IDbDependencyResolver を実装し、登録されている各 DbProviderServices 型が "既定の競合回避モジュール" として追加されるため、これが可能です。 IDbDpendencyResolver メカニズムの詳細については、「 [依存関係の解決](xref:ef6/fundamentals/configuring/dependency-resolution)」を参照してください。 ただし、プロバイダーのその他のサービスを解決するために、この仕様のすべての概念を理解する必要はありません。

プロバイダーが追加のサービスを解決するための最も一般的な方法は、DbProviderServices クラスのコンストラクター内の各サービスに対して DbProviderServices を呼び出すことです。 たとえば、SqlProviderServices (SQL Server 用の EF プロバイダー) では、初期化のために次のようなコードが使用されます。

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

このコンストラクターは、次のヘルパークラスを使用します。

*   SingletonDependencyResolver: シングルトンサービス、つまり、GetService が呼び出されるたびに同じインスタンスが返されるサービスを解決するための簡単な方法を提供します。 一時的なサービスは、必要に応じて一時的なインスタンスを作成するために使用されるシングルトンファクトリとして登録されることがよくあります。
*   ExecutionStrategyResolver: IExecutionStrategy 実装を返すための競合回避モジュール。

DbProviderServices を使用する代わりに、DbProviderServices GetService をオーバーライドし、追加のサービスを直接解決することもできます。 このメソッドは、EF が特定の型で定義されたサービス (場合によっては特定のキー) を必要とするときに呼び出されます。 このメソッドは、可能であればサービスを返すか、または null を返してサービスを返し、別のクラスで解決することを許可する必要があります。 たとえば、既定の接続ファクトリを解決するには、GetService のコードが次のようになります。

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>登録順序

アプリケーションの構成ファイルに複数の DbProviderServices 実装が登録されている場合は、一覧に示されている順序でセカンダリリゾルバーとして追加されます。 リゾルバーは常にセカンダリリゾルバーチェーンの最上位に追加されるため、一覧の最後にあるプロバイダーは、他の依存関係を解決する機会を得ることになります。 (この方法は、最初は少し直感的にわかりにくいかもしれませんが、一覧から各プロバイダーを取得し、既存のプロバイダーの上に積み重ねることを想像する場合は意味があります)。

ほとんどのプロバイダーサービスはプロバイダー固有であり、プロバイダーの不変名によってキーが指定されているため、この順序付けは通常は問題になりません。 ただし、プロバイダーの不変名またはその他のプロバイダー固有のキーでキー付けされていないサービスでは、この順序に基づいてサービスが解決されます。 たとえば、別の場所で明示的に設定されていない場合、既定の接続ファクトリはチェーン内の最上位のプロバイダーから取得されます。

## <a name="additional-config-file-registrations"></a>追加の構成ファイルの登録

上記で説明した追加のプロバイダーサービスの一部は、アプリケーションの構成ファイルに明示的に登録することができます。 この操作を実行すると、DbProviderServices 実装の GetService メソッドによって返されるものの代わりに、構成ファイルでの登録が使用されます。

### <a name="registering-the-default-connection-factory"></a>既定の接続ファクトリを登録しています

EF5 以降では、EntityFramework NuGet パッケージによって、SQL Express 接続ファクトリまたは LocalDb 接続ファクトリが構成ファイルに自動的に登録されます。

次に例を示します。

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

この _型_ は、既定の接続ファクトリのアセンブリ修飾型名であり、IDbConnectionFactory を実装する必要があります。

プロバイダーの NuGet パッケージをインストールするときに、この方法で既定の接続ファクトリを設定することをお勧めします。 以下 _のプロバイダーの NuGet パッケージを_ 参照してください。

## <a name="additional-ef6-provider-changes"></a>その他の EF6 プロバイダーの変更

### <a name="spatial-provider-changes"></a>空間プロバイダーの変更

空間型をサポートするプロバイダーは、DbSpatialDataReader から派生したクラスにいくつかの追加のメソッドを実装する必要があります。

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

また、同期メソッドへの既定の実装デリゲートとしてオーバーライドすることをお勧めする既存のメソッドの新しい非同期バージョンもあります。したがって、非同期的に実行することはできません。

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>列挙可能なネイティブサポート。 Contains

EF6 には、列挙型の使用に関するパフォーマンスの問題に対処するために追加された新しい式の種類 DbInExpression が導入されています。は LINQ クエリ内に含まれています。 DbProviderManifest クラスには、新しい仮想メソッドである SupportsInExpression があります。これは、プロバイダーが新しい式の型を処理するかどうかを判断するために EF によって呼び出されます。 既存のプロバイダー実装との互換性のために、メソッドは false を返します。 この改善点を活用するために、EF6 プロバイダーは DbInExpression を処理するコードを追加し、SupportsInExpression をオーバーライドして true を返すことができます。 DbInExpression のインスタンスは、DbExpressionBuilder.In メソッドを呼び出すことによって作成できます。 DbInExpression インスタンスは、通常テーブル列を表す DbExpression と、一致するかをテストするための DbConstantExpression のリストで構成されます。

## <a name="nuget-packages-for-providers"></a>プロバイダーの NuGet パッケージ

EF6 プロバイダーを利用できるようにする方法の1つは、NuGet パッケージとしてリリースすることです。 NuGet パッケージを使用すると、次のような利点があります。

*   NuGet を使用すると、プロバイダーの登録をアプリケーションの構成ファイルに簡単に追加できます。
*   規則によって行われる接続が登録済みのプロバイダーを使用するように、構成ファイルに追加の変更を加えて、既定の接続ファクトリを設定することができます。
*   新しい EF パッケージがリリースされた後も EF6 プロバイダーが引き続き機能するように、NuGet はバインドリダイレクトの追加を処理します。

この例として、 [オープンソースのコードベース](https://github.com/aspnet/entityframework6)に含まれている entityframework パッケージがあります。 このパッケージには、EF プロバイダーの NuGet パッケージを作成するための適切なテンプレートが用意されています。

### <a name="powershell-commands"></a>PowerShell コマンド

EntityFramework NuGet パッケージをインストールすると、プロバイダーパッケージに非常に便利な2つのコマンドを含む PowerShell モジュールが登録されます。

*   [追加] を使用すると、ターゲットプロジェクトの構成ファイルにプロバイダーの新しいエンティティが追加され、登録されているプロバイダーの一覧の末尾にあることが確認されます。
*   追加-EFDefaultConnectionFactory は、ターゲットプロジェクトの構成ファイルで defaultConnectionFactory の登録を追加または更新します。

これらのコマンドはいずれも、entityFramework セクションを構成ファイルに追加し、必要に応じてプロバイダーコレクションを追加します。

これらのコマンドは install.ps1 NuGet スクリプトから呼び出されることを意図しています。 たとえば、SQL Compact プロバイダーの install.ps1 は次のようになります。

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

これらのコマンドの詳細については、パッケージマネージャーコンソールウィンドウの get-help を使用して取得できます。

## <a name="wrapping-providers"></a>プロバイダーのラップ

ラッピングプロバイダーは、既存のプロバイダーをラップして、プロファイリングやトレースなどの他の機能と共に拡張する EF や ADO.NET プロバイダーです。 ラッププロバイダーは通常の方法で登録できますが、多くの場合、プロバイダー関連のサービスの解決をインターセプトして、実行時にラッピングプロバイダーを設定する方が便利です。 これを行うには、DbConfiguration クラスの静的イベント Onロック構成を使用できます。

Onlocked Configuration は、EF によって、アプリケーションドメインのすべての EF 構成がから取得され、使用のためにロックされる前に呼び出された後に呼び出されます。 アプリの起動時 (EF が使用される前) に、アプリはこのイベントのイベントハンドラーを登録する必要があります。 (ここでは、このハンドラーを構成ファイルに登録するためのサポートを追加することを検討していますが、これはまだサポートされていません)。その後、イベントハンドラーは、ラップする必要があるすべてのサービスに対して、交換用 Eservice を呼び出す必要があります。  

たとえば、IDbConnectionFactory と DbProviderService をラップするには、次のようなハンドラーを登録する必要があります。

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

解決済みで、サービスを解決するために使用されたキーと共にラップされる必要があるサービスは、ハンドラーに渡されます。 ハンドラーはこのサービスをラップし、返されたサービスをラップされたバージョンに置き換えることができます。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>EF を使用した DbProviderFactory の解決

DbProviderFactory は、前の「 _プロバイダーの種類の概要_ 」で説明されているように、EF で必要とされる基本的なプロバイダーの種類の1つです。 既に説明したように、これは EF 型ではなく、通常は EF 構成には含まれませんが、machine.config ファイルやアプリケーションの構成ファイルでの通常の ADO.NET プロバイダーの登録です。

ただし、この EF では、使用する DbProviderFactory を検索するときに通常の依存関係の解決メカニズムが使用されます。 既定の競合回避モジュールでは、構成ファイルで通常の ADO.NET 登録が使用されるため、これは通常は透過的です。 ただし、通常の依存関係の解決メカニズムが使用されているため、通常の ADO.NET 登録が完了していない場合でも、IDbDependencyResolver を使用して DbProviderFactory を解決できることを意味します。

この方法で DbProviderFactory を解決すると、いくつかの影響があります。

*   コードベースの構成を使用するアプリケーションでは、DbConfiguration クラスに呼び出しを追加して、適切な DbProviderFactory を登録できます。 これは、ファイルベースの構成をまったく使用しない (または利用できない) アプリケーションに特に役立ちます。
*   前の「 _プロバイダーのラップ_ 」セクションで説明されているように、サービスは置き換えることができます。
*   理論的には、DbProviderServices の実装によって DbProviderFactory が解決する可能性があります。

これらのいずれかを実行する際に注意する必要がある重要な点は、EF による DbProviderFactory の参照のみに影響することです。 EF 以外の他のコードでは、ADO.NET プロバイダーが通常の方法で登録されることを期待している場合があります。登録が見つからない場合は、失敗する可能性があります。 このため、通常は DbProviderFactory を通常の ADO.NET 方法で登録する方が適しています。

### <a name="related-services"></a>関連サービス

EF を使用して DbProviderFactory を解決する場合は、IProviderInvariantName および IDbProviderFactoryResolver サービスも解決する必要があります。

IProviderInvariantName は、特定の種類の DbProviderFactory に対してプロバイダーの不変名を決定するために使用されるサービスです。 このサービスの既定の実装では、ADO.NET プロバイダーの登録が使用されます。 これは、DbProviderFactory が EF によって解決されているために ADO.NET プロバイダーが通常の方法で登録されていない場合にも、このサービスを解決する必要があることを意味します。 DbConfiguration. SetProviderFactory メソッドを使用すると、このサービスのリゾルバーが自動的に追加されることに注意してください。

前の「 _プロバイダーの種類の概要_ 」で説明したように、IDbProviderFactoryResolver は、指定された DbConnection オブジェクトから正しい DbProviderFactory を取得するために使用されます。 .NET 4 で実行する場合のこのサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。 これは、DbProviderFactory が EF によって解決されているために ADO.NET プロバイダーが通常の方法で登録されていない場合にも、このサービスを解決する必要があることを意味します。
