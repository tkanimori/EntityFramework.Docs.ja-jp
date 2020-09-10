---
title: 依存関係の解決-EF6
description: Entity Framework 6 の依存関係の解決
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: c23253dc5a413077e3980fcfa18ea83b5fc3970e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618423"
---
# <a name="dependency-resolution"></a>依存関係の解決
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

EF6 以降、Entity Framework には、必要なサービスの実装を取得するための汎用機構が含まれています。 つまり、EF が一部のインターフェイスまたは基本クラスのインスタンスを使用する場合、使用するインターフェイスまたは基本クラスの具象実装を要求します。 これは、IDbDependencyResolver インターフェイスを使用して実現されます。  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

GetService メソッドは、通常 EF によって呼び出され、EF またはアプリケーションによって提供される IDbDependencyResolver の実装によって処理されます。 型引数が呼び出されると、要求されているサービスのインターフェイスまたは基本クラスの型になります。キーオブジェクトは、null または要求されたサービスに関するコンテキスト情報を提供するオブジェクトのいずれかになります。  

特に指定がない限り、返されるオブジェクトはシングルトンとして使用できるため、スレッドセーフである必要があります。 多くの場合、返されるオブジェクトはファクトリであり、ファクトリ自体はスレッドセーフである必要がありますが、ファクトリから返されるオブジェクトは、使用するたびにファクトリから新しいインスタンスが要求されるため、スレッドセーフである必要はありません。

この記事には、IDbDependencyResolver を実装する方法の詳細は含まれていませんが、代わりに、EF が GetService を呼び出すサービスの種類 (インターフェイスと基本クラスの型)、およびこれらの各呼び出しのキーオブジェクトのセマンティクスを参照として機能します。

## <a name="systemdataentityidatabaseinitializertcontext"></a>Data. Entity. IDatabaseInitializer<TContext\>  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 指定されたコンテキスト型のデータベース初期化子  

**キー**: 使用されていません。null になります  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>Func は MigrationSqlGenerator を<します。\>  

**導入**されたバージョン: ef 6.0.0

**返されるオブジェクト**: データベースを作成するために使用できる SQL ジェネレーターを作成するファクトリ (データベース初期化子を使用したデータベースの作成など)。  

**Key**: SQL が生成されるデータベースの種類を指定する ADO.NET プロバイダーの不変名を含む文字列。 たとえば、キー "system.string" に対して SQL Server SQL ジェネレーターが返されます。  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdataentitycorecommondbproviderservices"></a>DbProviderServices.............  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 指定したプロバイダーの不変名に使用する EF プロバイダー  

**Key**: プロバイダーが必要なデータベースの種類を指定する ADO.NET プロバイダーの不変名を含む文字列。 たとえば、キー "system.string" に対して SQL Server プロバイダーが返されます。  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>IDbConnectionFactory (システムのデータ)  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: EF が規約に従ってデータベース接続を作成するときに使用される接続ファクトリ。 つまり、EF に接続文字列または接続文字列が指定されておらず、app.config または web.config に接続文字列が見つからない場合は、このサービスを使用して、規則に従って接続を作成します。 接続ファクトリを変更すると、EF が既定で別の種類のデータベース (SQL Server Compact Edition など) を使用できるようになります。  

**キー**: 使用されていません。null になります  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>IManifestTokenService (システムのデータ)  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: 接続からプロバイダーマニフェストトークンを生成できるサービス。 通常、このサービスは2つの方法で使用されます。 まず、モデルを構築するときにデータベースに接続する Code First を避けるために使用できます。 次に、特定のデータベースバージョンのモデルを強制的に Code First するために使用できます。たとえば、SQL Server 2008 が使用されている場合でも、SQL Server 2005 のモデルを強制的に作成することができます。  

**オブジェクトの有効期間**: シングルトン: 同じオブジェクトが複数回使用され、異なるスレッドによって同時に使用される場合があります。  

**キー**: 使用されていません。null になります  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>IDbProviderFactoryService (システムのデータ)  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 特定の接続からプロバイダーファクトリを取得できるサービス。 .NET 4.5 では、プロバイダーは接続からパブリックにアクセスできます。 .NET 4 では、このサービスの既定の実装は、特定のヒューリスティックを使用して一致するプロバイダーを検索します。 これらのエラーが発生した場合は、適切な解決策を提供するために、このサービスの新しい実装を登録できます。  

**キー**: 使用されていません。null になります  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<DbContext、IDbModelCacheKey になります。\>  

**導入**されたバージョン: ef 6.0.0  

**返さ**れたオブジェクト: 特定のコンテキストのモデルキャッシュキーを生成するファクトリ。 既定では、EF は、プロバイダーごとに DbContext 型ごとに1つのモデルをキャッシュします。 このサービスの別の実装を使用して、スキーマ名などの他の情報をキャッシュキーに追加できます。  

**キー**: 使用されていません。null になります  

## <a name="systemdataentityspatialdbspatialservices"></a>DbSpatialServices (システムのデータ)  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: geography および geometry 空間型の基本 ef プロバイダーにサポートを追加する ef 空間プロバイダー。  

**キー**: dbsptialservices は2つの方法で要求されます。 最初に、キーとして DbProviderInfo オブジェクト (不変名とマニフェストトークンを含む) を使用して、プロバイダー固有の空間サービスが要求されます。 2つ目は、キーなしで DbSpatialServices を要求できることです。 これは、スタンドアロンの DbGeography 型または Dbgeography 型を作成するときに使用される "グローバル空間プロバイダー" を解決するために使用されます。  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func は IDbExecutionStrategy を<します。\>  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: データベースに対してクエリとコマンドが実行されたときに、プロバイダーが再試行またはその他の動作を実装できるようにするサービスを作成するファクトリ。 実装が指定されていない場合、EF は単にコマンドを実行し、スローされた例外をすべて伝達します。 SQL Server は、このサービスを使用して再試行ポリシーを提供します。これは、SQL Azure などのクラウドベースのデータベースサーバーに対して実行する場合に特に便利です。  

**Key**: プロバイダーの不変名と、必要に応じて実行戦略を使用するサーバー名を含む ExecutionStrategyKey オブジェクトです。  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection、string、または system.string です。履歴コンテキスト\>  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: プロバイダーが、 `__MigrationHistory` EF の移行で使用されるテーブルへの履歴コンテキストのマッピングを構成できるようにするファクトリ。 履歴コンテキストは Code First DbContext であり、テーブルの名前や列マッピングの仕様などを変更するために通常の fluent API を使用して構成できます。  

**キー**: 使用されていません。null になります  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdatacommondbproviderfactory"></a>DbProviderFactory (システム)  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 指定したプロバイダーの不変名に使用する ADO.NET プロバイダー。  

**Key**: ADO.NET プロバイダーの不変名を含む文字列  

>[!NOTE]
> 既定の実装では通常の ADO.NET プロバイダー登録が使用されるため、このサービスは通常は直接変更されません。 EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>IProviderInvariantName (システムのデータ)  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 特定の種類の DbProviderFactory に対してプロバイダーの不変名を決定するために使用されるサービス。 このサービスの既定の実装では、ADO.NET プロバイダーの登録が使用されます。 これは、DbProviderFactory が EF によって解決されているために ADO.NET プロバイダーが通常の方法で登録されていない場合にも、このサービスを解決する必要があることを意味します。  

**キー**: 不変名を必要とする DbProviderFactory インスタンス。  

>[!NOTE]
> EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System.string...............................  

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: 事前に生成されたビューを含むアセンブリのキャッシュ。 置換は通常、検出を行わずに、事前に生成されたビューを含むアセンブリを EF に知らせるために使用されます。  

**キー**: 使用されていません。null になります  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>複数形化. IPluralizationService (その他)

**導入**されたバージョン: ef 6.0.0  

**返されたオブジェクト**: EF によって複数化と単数の名前に使用されるサービス。 既定では、英語の複数形化サービスが使用されます。  

**キー**: 使用されていません。null になります  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>IDbInterceptor のようになります。  

**導入**されたバージョン: ef 6.0.0

**返されるオブジェクト**: アプリケーションの起動時に登録する必要があるインターセプター。 これらのオブジェクトは GetServices 呼び出しを使用して要求され、依存関係競合回避モジュールによって返されるすべてのインターセプターが登録されることに注意してください。

**キー**: 使用されていません。null になります。  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<system.object。 DbContext、Action<string、system.string、. \> DatabaseLogFormatter ですが、この関数は\>  

**導入**されたバージョン: ef 6.0.0  

**返されるオブジェクト**: コンテキストで使用されるデータベースログフォーマッタを作成するために使用されるファクトリ。指定されたコンテキストに対してデータベース .Log プロパティが設定されています。  

**キー**: 使用されていません。null になります。  

## <a name="funcsystemdataentitydbcontext"></a>Func<system.object の DbContext\>  

**導入**されたバージョン: ef 6.1.0  

**返されるオブジェクト**: コンテキストにアクセス可能なパラメーターなしのコンストラクターがない場合に、移行用のコンテキストインスタンスを作成するために使用されるファクトリ。  

**Key**: ファクトリが必要な派生 dbcontext の型の型オブジェクト。  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func は IMetadataAnnotationSerializer を<しています。\>  

**導入**されたバージョン: ef 6.1.0  

**返されたオブジェクト**: 厳密に型指定されたカスタム注釈をシリアル化するためにシリアライザーを作成するために使用されるファクトリ。 Code First Migrations で使用するために、XML にシリアル化して destを認識することができます。  

**キー**: シリアル化または逆シリアル化される注釈の名前。  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func<system.object... Entity. Infrastructure\>  

**導入**されたバージョン: ef 6.1.0  

**返されるオブジェクト**: トランザクションのハンドラーを作成するために使用されるファクトリ。これにより、コミットエラーの処理などの状況に対して特別な処理を適用できます。  

**Key**: ExecutionStrategyKey オブジェクト。プロバイダーの不変名と、必要に応じて、トランザクションハンドラーを使用するサーバー名を格納します。  
