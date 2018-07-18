---
title: Entity Framework 6 のプロバイダー モデル - EF6
author: divega
ms.date: 2018-06-27
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
caps.latest.revision: 3
ms.openlocfilehash: 49b655bdbe1b256b7de517edec84945d1ee06f79
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121940"
---
# <a name="the-entity-framework-6-provider-model"></a>Entity Framework 6 のプロバイダー モデル

Entity Framework プロバイダー モデルでは、さまざまな種類のデータベース サーバーで使用する Entity Framework を使用します。 たとえば、EF に EF に Microsoft SQL Server Compact Edition に対して使用を許可する別のプロバイダーにプラグインできるときに、Microsoft SQL Server に対して使用を許可する 1 つのプロバイダーを接続することができます。 認識しています EF6 のプロバイダーが見つかりません、 [Entity Framework プロバイダー](~/ef6/fundamentals/providers/index.md)ページ。

特定の変更は、EF がオープン ソース ライセンスに基づいてリリースする EF を許可するプロバイダーと対話する方法に必要でした。 これらの変更は、プロバイダーの登録のための新しいメカニズムと EF6 のアセンブリに対して EF プロバイダーの再構築が必要です。

## <a name="rebuilding"></a>再構築

EF6 では、.NET Framework の一部であった以前中核のコードが帯域外の (OOB) のアセンブリとしてようになりました配送されます。 EF6 に対してアプリケーションを構築する方法の詳細についてで確認できます、 [EF6 のアプリケーションの更新](~/ef6/what-is-new/upgrading-to-ef6.md)ページ。 プロバイダーは、次の手順を使用して再構築する必要があります。

## <a name="provider-types-overview"></a>プロバイダーの種類の概要

EF プロバイダーは、実際に、これらのサービス (の基本クラス) から拡張または (インターフェイス) を実装する CLR 型で定義されているプロバイダーに固有のサービスのコレクションです。 これらのサービスの 2 つは、基本的なと EF で機能するために必要です。 他のユーザーは省略可能ですし、のみ、特定の機能が必要な場合やこれらのサービスの既定の実装は機能しません、特定のデータベース サーバーを対象となる場合は、実装する必要があります。

### <a name="fundamental-provider-types"></a>基本的なプロバイダーの種類

#### <a name="dbproviderfactory"></a>DbProviderFactory

派生した型に依存して EF [System.Data.Common.DbProviderFactory](http://msdn.microsoft.com/en-us/library/system.data.common.dbproviderfactory.aspx)低レベルのデータベースのすべてのアクセスを実行するためです。 DbProviderFactory が EF の一部ではありませんが、ADO.NET プロバイダーのエントリ ポイントを提供する .NET Framework のクラスまたはアプリケーションによって直接 EF、他の O/RMs によって接続、コマンド、パラメーターのインスタンスを取得して使用する代わりには、他のプロバイダーで ADO.NET の抽象化に依存しない方法です。 DbProviderFactory の詳細についてに記載されて、 [ADO.NET に関する MSDN ドキュメント](http://msdn.microsoft.com/en-us/library/a6cd7c08.aspx)します。

#### <a name="dbproviderservices"></a>DbProviderServices

EF は、EF によって既に ADO.NET プロバイダーによって提供される機能の上に必要な追加機能を提供するため DbProviderServices から派生した型があることに依存します。 以前のバージョンの EF で DbProviderServices クラスは、.NET Framework の一部であったし、System.Data.Common 名前空間が見つかりました。 このクラスを EF6 以降、EntityFramework.dll の一部になったし、System.Data.Entity.Core.Common 名前空間にあります。

DbProviderServices の実装の基本的な機能の詳細についてはのあります[MSDN](http://msdn.microsoft.com/en-us/library/ee789835.aspx)します。 ただし、この情報の書き込みの時点では更新されないこと EF6 の概念のほとんどは現在も有効です。 DbProviderServices の SQL Server と SQL Server Compact の実装にチェックインしても、[オープン ソース コードベース](https://gihtub.com/aspnet/EntityFramework6/)おり、その他の実装のための便利な参考として使用できます。

以前のバージョンの EF で DbProviderServices の実装を使用する ADO.NET プロバイダーから直接入手されました。 これは、IServiceProvider を DbProviderFactory をキャストして、GetService メソッドを呼び出すことで行われました。 これは、EF のプロバイダーを DbProviderFactory に密結合します。 この結合では、.NET Framework 外に移動される EF がブロックされているし、この密結合を EF6 のために削除されましたが、アプリケーションの構成ファイルで直接、またはコード ベースで DbProviderServices の実装が登録されましたさらに詳しく記載されている構成、_登録 DbProviderServices_以下のセクション。

### <a name="additional-services"></a>その他のサービス

上記で説明した基本的なサービスだけでなくも多くの他のサービスが EF によって使用される常に、または場合によってプロバイダーに固有であります。 DbProviderServices の実装が、これらのサービスの既定のプロバイダーに固有の実装を指定することができます。 アプリケーションはこれらのサービスの実装をオーバーライドしたり、DbProviderServices の一種で、既定値が提供されない場合に、実装を提供できます。 これはで詳しく説明されている、_追加サービスを解決する_以下のセクション。

プロバイダーは、プロバイダーを対象にする必要があります追加サービスの種類は、以下に示します。 詳細については、各サービスの種類は、API のドキュメントに記載されています。

#### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

これは、オプションのサービスにより、クエリとコマンドは、データベースに対して実行されたときに再試行する場合やその他の動作を実装するプロバイダーです。 実装が指定されていない場合 EF は単にコマンドを実行し、スローされた例外を伝達します。 SQL Server のこのサービスは、SQL Azure などのクラウド ベースのデータベース サーバーに対して実行したときに特に便利ですが、再試行ポリシーを提供する使用されます。

#### <a name="idbconnectionfactory"></a>IDbConnectionFactory

これは、プロバイダーにデータベース名のみが指定されると、規約によって DbConnection オブジェクトを作成することができる省略可能なサービスです。 このサービス DbProviderServices の実装、EF 4.1 以降に存在していたことができますも明示的に設定する構成ファイルまたはコードによって解決できることに注意してください。 プロバイダーは、既定のプロバイダーとして登録されている場合は、このサービスを解決する機会のみを取得 (を参照してください_既定プロバイダー_以下)、既定の接続ファクトリが設定されていない別の場所。

#### <a name="dbspatialservices"></a>DbSpatialServices

これは、geography および geometry 空間型のサポートを追加するプロバイダーを許可するオプションのサービスです。 このサービスの実装は、空間型で EF を使用するアプリケーションの順序で指定する必要があります。 DbSptialServices には、2 つの方法での確認が表示されます。 DbProviderInfo オブジェクトを使用して最初に、プロバイダー固有の空間サービスが要求された (不変式が含まれていますとマニフェスト トークン) としてキー。 第 2 に、DbSpatialServices 要求できるキーを持たない。 これは、解決するには、"グローバル空間"に使用されるプロバイダー スタンドアロン DbGeography または DbGeometry の種類を作成するときに使用されます。

#### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

これは、Code First での作成とデータベース スキーマの変更に使用する SQL の生成に使用される、EF の移行を許可するオプションのサービスです。 移行をサポートするために実装が必要です。 実装が指定されている場合に、または使用データベース初期化子または Database.Create メソッドを使用してデータベースを作成するとき。

#### <a name="funcdbconnection-string-historycontextfactory"></a>Func < DbConnection、文字列、HistoryContextFactory >

これは省略可能なサービスであり、プロバイダーに HistoryContext のマッピングを構成できるように、 `__MigrationHistory` EF の移行で使用されるテーブル。 HistoryContext コードの最初の DbContext であり、名前のテーブルと列マッピングの指定などを変更する通常の fluent API を使用して構成できます。 すべてのプロバイダー、EF によって返されるこのサービスの既定の実装は、そのプロバイダーによってすべて既定のテーブルと列のマッピングがサポートされている場合、特定のデータベース サーバーの動作可能性があります。 このような場合はこのサービスの実装を提供する、プロバイダーは必要ありません。

#### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

これは、指定した DbConnection オブジェクトから正しい DbProviderFactory を取得するためのオプションのサービスです。 すべてのプロバイダー、EF によって返されるこのサービスの既定の実装の目的は、すべてのプロバイダーを使用します。 ただし、.NET 4 で実行するときに、DbProviderFactory が公開されていない場合に 1 つからその DbConnections します。 そのため、EF はいくつかのヒューリスティックを使用して、検索、検索する登録済みのプロバイダー。 一部のプロバイダーは、これらのヒューリスティックの失敗をこのような状況で、プロバイダーが新しい実装を提供します。 ことができます。

## <a name="registering-dbproviderservices"></a>DbProviderServices の登録

DbProviderServices の実装を使用するは、アプリケーションの構成ファイル (app.config または web.config) またはコード ベースの構成を使用して登録できます。 いずれの場合も、登録は、キーとして、プロバイダーの「固定名」を使用します。 これにより、複数のプロバイダーを登録し、1 つのアプリケーションで使用できます。 EF の登録のために使用不変名は、インバリアント ADO.NET プロバイダーの登録と接続文字列に使用される名前と同じです。 たとえば、SQL Server 固定名"System.Data.SqlClient"が使用されます。

### <a name="config-file-registration"></a>構成ファイルの登録

使用する DbProviderServices の種類は、アプリケーションの構成ファイルの entityFramework セクションのプロバイダーの一覧で、provider 要素として登録されます。 例えば:

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_型_文字列を使用する DbProviderServices の実装のアセンブリ修飾型名を指定する必要があります。

### <a name="code-based-registration"></a>コード ベースの登録

以降では、EF6 プロバイダー登録することもコードを使用します。 これにより、アプリケーションの構成ファイルに変更を加えずに使用される、EF のプロバイダー。 コード ベースの構成を使用するアプリケーション クラスを作成する DbConfiguration」の説明に従って、[コード ベースの構成ドキュメント](http://msdn.com/data/jj680699)します。 DbConfiguration クラスのコンス トラクターでは、EF のプロバイダーを登録する SetProviderServices を呼び出す必要がありますから。 例えば:

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>その他のサービスを解決します。

上記のとおり、_プロバイダーの型の概要_セクション、DbProviderServices クラスが追加サービスを解決することもできます。 DbProviderServices が IDbDependencyResolver を実装し、登録されている各 DbProviderServices タイプが「既定のリゾルバー」として追加するため、可能です。 IDbDpendencyResolver メカニズムがで詳しく説明されている[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)します。 ただし、プロバイダーの追加サービスを解決するには、この仕様でのすべての概念を理解する必要はありません。

その他のサービスを解決するのには、プロバイダーの最も一般的な方法を DbProviderServices クラスのコンス トラクター内の各サービスの DbProviderServices.AddDependencyResolver を呼び出します。 たとえば、SqlProviderServices (SQL Server 用の EF プロバイダー) では、初期化のための次のようなコードがあります。

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

このコンス トラクターは、次のヘルパー クラスを使用します。

*   SingletonDependencyResolver: 単一のサービスを解決する簡単な方法を提供する、同じインスタンスが返されるたびにその GetService が呼び出されるサービスは、します。 一時的なサービスは、オンデマンドで一時的なインスタンスを作成するために使用するシングルトン ファクトリとして多くの場合、登録されます。
*   ExecutionStrategyResolver: リゾルバーを返す IExecutionStrategy 実装に固有です。

DbProviderServices.AddDependencyResolver を使用する代わりに、DbProviderServices.GetService をオーバーライドし、その他のサービスを直接解決することはも。 EF には、特定の種類、および場合によっては、指定されたキーの定義、サービスが必要がある場合、このメソッドが呼び出すされます。 場合、またはサービスを返すことのオプトアウトする場合は null を返すし、問題を解決する別のクラスを許可する代わりに、メソッドは、サービスを返す必要があります。 既定の接続ファクトリを解決するのには GetService 内のコード例このようなもの。

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

アプリケーションの構成ファイルで複数の DbProviderServices の実装が登録されている場合は、記載されている順序でセカンダリ競合回避モジュールとして追加されます。 競合回避モジュールは、常に、これには、一覧の最後に、プロバイダーが、他のユーザーの前に依存関係を解決するチャンスを取得することを意味セカンダリ競合回避モジュールのチェーンの先頭に追加されます。 (最初は、逆ほとんど感じられることが、一覧から、各プロバイダーを取得し、既存のプロバイダーの上に積み重ねを想像している場合に理にかなって)。

この順序は、通常は関係ありませんプロバイダー サービスのほとんどはプロバイダー固有とプロバイダー固定名によってキー付きであるため。 ただし、サービスが付いていませんプロバイダーの不変名またはいくつか他のプロバイダーに固有のキーで、サービスが解決されますこの順序に基づきます。 たとえばに設定されていない場合に明示的に異なる場所にそれ以外の場合、し、既定の接続ファクトリはチェーンの最上位のプロバイダーから提供されます。

## <a name="additional-config-file-registrations"></a>追加の構成ファイルの登録

アプリケーションの構成ファイルで直接の説明に従って追加のプロバイダーのサービスの一部を明示的に登録することになります。 DbProviderServices の実装の GetService メソッドによって返されるものではなく、構成ファイル内にある登録が完了したらこのが使用されます。

### <a name="registering-the-default-connection-factory"></a>既定の接続ファクトリを登録します。

構成ファイルで SQL Express 接続ファクトリ、または LocalDb 接続ファクトリを登録する自動的に EntityFramework NuGet パッケージを EF5 で起動します。

例えば:

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

_型_IDbConnectionFactory を実装する必要があります既定の接続ファクトリのアセンブリ修飾型名です。

プロバイダーの NuGet パッケージがインストールされている場合は、この方法で、既定の接続ファクトリを設定することをお勧めします。 参照してください_プロバイダー用の NuGet パッケージ_以下。

## <a name="additional-ef6-provider-changes"></a>EF6 プロバイダーの追加の変更

### <a name="spatial-provider-changes"></a>空間のプロバイダーの変更

空間型をサポートするプロバイダーでは、DbSpatialDataReader から派生したクラスに対して追加のメソッドを実装する必要があります。

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

既定の実装が同期メソッドに委任し、そのため実行されなかった非同期的に、オーバーライドする推奨される既存のメソッドの非同期バージョンを新しいはもあります。

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Enumerable.Contains のネイティブ サポート

EF6 では、新しい式の型での LINQ クエリ Enumerable.Contains の使用に関するパフォーマンス問題に対処に追加された、DbInExpression について説明します。 DbProviderManifest クラスには、新しい仮想メソッドは、プロバイダーが新しい式の型を処理するかどうか、EF によって呼び出され、SupportsInExpression があります。 既存のプロバイダーの実装と互換性のため、メソッドは false を返します。 この機能強化のメリットを EF6 プロバイダーは、DbInExpression を処理し、true を返す SupportsInExpression をオーバーライドするコードを追加できます。 DbExpressionBuilder.In メソッドを呼び出して DbInExpression のインスタンスを作成できます。 DbInExpression インスタンスは、通常、テーブルの列との一致をテストする DbConstantExpression の一覧を表す、dbexpression オブジェクトで構成されます。

## <a name="nuget-packages-for-providers"></a>プロバイダーの NuGet パッケージ

EF6、プロバイダーを使用できるようにする方法の 1 つでは、NuGet パッケージとしてリリースします。 NuGet パッケージを使用すると、次の利点があります。

*   使いやすい NuGet プロバイダーの登録をアプリケーションの構成ファイルに追加するには
*   規則によって行われる接続は、登録済みのプロバイダーを使用するために、既定の接続ファクトリを設定する構成ファイルに追加の変更を行んだことができます。
*   NuGet を処理できるように、EF6 のプロバイダーは、新しい EF パッケージがリリースされた後でもの作業を続行する必要があります、バインド リダイレクトを追加します。

この例は、EntityFramework.SqlServerCompact パッケージに含まれている、[オープン ソース コードベース](http://github.com/aspnet/entityframework6)します。 このパッケージは、EF プロバイダー NuGet パッケージを作成するための適切なテンプレートを提供します。

### <a name="powershell-commands"></a>PowerShell コマンド

EntityFramework NuGet パッケージがインストールされている場合は、プロバイダーのパッケージの非常に便利な 2 つのコマンドを含む PowerShell モジュールを登録します。

*   追加 EFProvider がターゲット プロジェクトの構成ファイルでプロバイダーの新しいエンティティを追加、登録されているプロバイダーの一覧の最後にあることを確認します。
*   追加 EFDefaultConnectionFactory いずれか、追加、またはターゲット プロジェクトの構成ファイルで defaultConnectionFactory 登録更新します。

構成ファイルを entityFramework セクションを追加し、必要な場合は、プロバイダーのコレクションを追加する 2 つのコマンドを処理します。

これらのコマンドは、install.ps1 NuGet スクリプトから呼び出すことが目的です。 たとえば、install.ps1 SQL Compact プロバイダーの次のようにします。

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

これらのコマンドの詳細については、パッケージ マネージャー コンソール ウィンドウで、get-help を使用して取得できます。

## <a name="wrapping-providers"></a>プロバイダーのラップ

折り返しプロバイダーは、既存のプロファイルやトレース機能などの他の機能と拡張プロバイダーをラップする EF または ADO.NET プロバイダーです。 通常の方法でプロバイダーのラップを登録できますが、詳細は、プロバイダーに関連するサービスの解像度をインターセプトすることで実行時に折り返しプロバイダーをセットアップすると便利です。 これを行うには、静的イベント OnLockingConfiguration DbConfiguration クラスを使用できます。

OnLockingConfiguration は使用がロックされる前に、EF がからのアプリ ドメインのすべての EF 構成の取得場所を決定した後に呼び出されます。 アプリの起動時に (前に EF を使用すると)、アプリが登録このイベントのイベント ハンドラー。 (構成ファイルにこのハンドラーを登録するためのサポートの追加を検討していますが、これはまだサポートされていません)。イベント ハンドラーは ReplaceService への呼び出しをラップする必要があるすべてのサービスにください。  

たとえば、IDbConnectionFactory と DbProviderService をラップするには、次のようにハンドラーを登録するか。

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

解決され、サービスの解決に使用されたキーと共に折り返す必要があるようになりましたが、サービスは、ハンドラーに渡されます。 ハンドラーはこのサービスをラップし、返されたサービスをラップされたバージョンに置き換えます。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>Ef の DbProviderFactory を解決します。

DbProviderFactory は」の説明に従って、EF で必要な基本的なプロバイダーの種類の 1 つ、_プロバイダーの型の概要_前のセクション。 既に述べたように、型ではない、EF と登録は通常、EF の構成の一部はありませんが代わりに、machine.config ファイルまたはアプリケーションの構成ファイルにおける通常の ADO.NET プロバイダーの登録。

この EF に関係なく引き続き使用の標準の依存関係の解決メカニズムの DbProviderFactory を探すときに使用します。 既定のリゾルバー構成ファイルで、通常の ADO.NET の登録を使用して、これは通常、透過的です。 メカニズムを使用する通常の依存関係の解決のため、IDbDependencyResolver を通常の ADO.NET の登録が行われていない場合でも、DbProviderFactory を解決するのには使用できることを意味します。

この方法で DbProviderFactory を解決するには、いくつかの意味合いがあります。

*   コード ベースの構成を使用するアプリケーションは、適切な DbProviderFactory を登録するには、その DbConfiguration クラスの呼び出しを追加できます。 これはしたくない (またはできません) をアプリケーションに特に便利だことをまったくの任意のファイル ベースの構成を使用します。
*   サービスがラップすることができますか」の説明に従って、ReplaceService を使用して置き換える、_プロバイダーのラップ_前のセクション
*   理論上は、DbProviderServices の実装の DbProviderFactory を解決する可能性があります。

これらのいずれかの方法について注意する重要な点は、EF によって DbProviderFactory の参照のみが影響します。 その他の EF 以外のコードでは、通常の方法で登録する ADO.NET プロバイダーに期待もと、登録が見つからない場合に失敗する可能性があります。 このため、通常の ADO.NET の方法で登録する DbProviderFactory の通常お勧めします。

### <a name="related-services"></a>関連サービス

DbProviderFactory を解決するのには EF を使用する場合、IProviderInvariantName と IDbProviderFactoryResolver サービスも解決される必要があります。

IProviderInvariantName は、特定の種類の DbProviderFactory のプロバイダーの不変名を判断するために使用するサービスです。 このサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。 つまり、DbProviderFactory が EF によって解決されるため、ADO.NET プロバイダーでは通常の方法で登録されていない場合もあるこのサービスを解決するために必要です。 このサービスの競合回避モジュールが DbConfiguration.SetProviderFactory メソッドを使用する場合を自動的に追加されたことに注意してください。

」の説明に従って、_プロバイダーの型の概要_上記セクションで、IDbProviderFactoryResolver は指定した DbConnection オブジェクトから正しい DbProviderFactory を取得するために使用します。 .NET 4 で実行されている場合は、このサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。 つまり、DbProviderFactory が EF によって解決されるため、ADO.NET プロバイダーでは通常の方法で登録されていない場合もあるこのサービスを解決するために必要です。
