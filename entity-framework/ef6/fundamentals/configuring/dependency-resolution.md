---
title: 依存関係の解決 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
ms.openlocfilehash: 45681bb0cedecd502b1968b90b7f682d3257dd23
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998163"
---
# <a name="dependency-resolution"></a>依存関係の解決
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

EF6 から始めて、Entity Framework には、必要なサービスの実装を取得するための汎用的なメカニズムが含まれています。 つまり、EF がいくつかのインターフェイスまたは基本クラスのインスタンスを使用する場合たずねられますインターフェイスまたは基本クラスの具象実装を使用します。 これは、IDbDependencyResolver インターフェイスを使用して実現されます。  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

GetService メソッドは、EF によって呼び出されます、IDbDependencyResolver EF またはアプリケーションのいずれかを指定の実装によって処理されます。 型引数が要求されているサービスのインターフェイスまたは基本クラス型で呼び出されると、およびキー オブジェクトが null または要求されたサービスに関するコンテキスト情報を提供するオブジェクト。  

この記事では IDbDependencyResolver を実装する方法の完全な詳細情報が含まれていないが、代わりにする EF の呼び出し GetService とキーのオブジェクトのセマンティクスのこれらの各サービスの種類 (つまり、インターフェイスおよび基本クラス型) への参照として機能します呼び出されます。 このドキュメントは最新の他のサービスが追加されるとします。  

## <a name="services-resolved"></a>サービスの解決  

限りをシングルトンとして使用できるため、返される任意のオブジェクトはスレッド セーフなをする必要があります。 多くの場合、ファクトリをこの例では、オブジェクトが返されます factory 自体はスレッド セーフである必要がありますが、ファクトリから返されるオブジェクトが使用するたびにファクトリから新しいインスタンスが要求されるので、スレッド セーフである必要はありません。  

### <a name="systemdataentityidatabaseinitializertcontext"></a>System.Data.Entity.IDatabaseInitializer < TContext\>  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 特定のコンテキストの種類のデータベース初期化子  

**キー**: いない使用は、null になります  

### <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>Func < System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\>  

**バージョンが導入された**: EF6.0.0

**返されるオブジェクト**: 移行とデータベース初期化子を含むデータベースの作成など、作成するデータベースを原因となるその他の操作に使用できる SQL ジェネレーターを作成するファクトリ。  

**キー**: SQL の生成対象となるデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。 たとえば、キー"System.Data.SqlClient"の SQL Server SQL ジェネレーターが返されます。  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdataentitycorecommondbproviderservices"></a>System.Data.Entity.Core.Common.DbProviderServices  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する EF プロバイダー  

**キー**: プロバイダーを必要とするデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。 たとえば、キー"System.Data.SqlClient"の SQL Server プロバイダーが返されます。  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System.Data.Entity.Infrastructure.IDbConnectionFactory  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 慣例 EF がデータベース接続を作成するときに使用する接続ファクトリ。 つまり、ef では、接続または接続文字列を指定しないと、app.config または web.config 接続文字列が見つかりません、し、このサービスは使用接続を作成する慣例します。 接続ファクトリを変更すると、既定では、さまざまな種類のデータベース (たとえば、SQL Server Compact Edition) を使用する EF を許可できます。  

**キー**: いない使用は、null になります  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System.Data.Entity.Infrastructure.IManifestTokenService  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 接続から、プロバイダー マニフェスト トークンを生成できるサービスです。 このサービスは通常、2 つの方法で使用されます。 最初に、Code First モデルを構築するときに、データベースへの接続を回避するために使用できます。 次に、Code First 場合でも、SQL Server 2008 を使用することがあります、SQL Server 2005 のモデルを強制する - たとえば、特定のデータベース バージョンのモデルの構築に強制的に使用できます。  

**オブジェクトの有効期間**: シングルトン - 同じオブジェクトは時間と別のスレッドで同時に使用される複数である可能性があります  

**キー**: いない使用は、null になります  

### <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System.Data.Entity.Infrastructure.IDbProviderFactoryService  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: サービスを特定の接続からプロバイダー ファクトリを取得できます。 .NET 4.5 では、プロバイダーは、接続からパブリックにアクセスできます。 このサービスの既定の実装では、.NET 4 で、一部のヒューリスティックを使用して、一致するプロバイダーを見つけます。 適切な解決を提供する場合、これらが失敗し、このサービスの新しい実装を登録できます。  

**キー**: いない使用は、null になります  

### <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func < DbContext、System.Data.Entity.Infrastructure.IDbModelCacheKey\>  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 指定されたコンテキストのモデルのキャッシュ キーを生成するファクトリ。 既定では、EF は、プロバイダーごとに DbContext 型ごとに 1 つのモデルをキャッシュします。 キャッシュ キーにスキーマ名などの他の情報を追加する、このサービスの別の実装を使用できます。  

**キー**: いない使用は、null になります  

### <a name="systemdataentityspatialdbspatialservices"></a>System.Data.Entity.Spatial.DbSpatialServices  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: geography および geometry 空間型の基本的な EF プロバイダーにサポートを追加する EF の空間プロバイダー。  

**キー**: DbSptialServices は 2 つの方法でのように求められます。 DbProviderInfo オブジェクトを使用して最初に、プロバイダー固有の空間サービスが要求された (不変式が含まれていますとマニフェスト トークン) をキーとして。 第 2 に、DbSpatialServices 要求できるキーを持たない。 これは、解決するには、"グローバル空間 provider"DbGeography または DbGeometry のスタンドアロン型の作成時に使用するに使用されます。  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func < System.Data.Entity.Infrastructure.IDbExecutionStrategy\>  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: サービスにより、クエリとコマンドは、データベースに対して実行されたときに再試行する場合やその他の動作を実装するプロバイダーを作成するファクトリ。 実装が指定されていない場合 EF は単にコマンドを実行し、スローされた例外を伝達します。 SQL Server のこのサービスは、SQL Azure などのクラウド ベースのデータベース サーバーに対して実行したときに特に便利ですが、再試行ポリシーを提供する使用されます。  

**キー**: プロバイダーの不変名と必要に応じて、実行方法を使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func < DbConnection、文字列、System.Data.Entity.Migrations.History.HistoryContext\>  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: プロバイダーを HistoryContext のマッピングを構成できるようにするファクトリ、 `__MigrationHistory` EF の移行で使用されるテーブル。 HistoryContext コードの最初の DbContext であり、名前のテーブルと列マッピングの指定などを変更する通常の fluent API を使用して構成できます。  

**キー**: いない使用は、null になります  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdatacommondbproviderfactory"></a>System.Data.Common.DbProviderFactory  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する ADO.NET プロバイダー。  

**キー**: ADO.NET プロバイダーの不変名を含む文字列  

>[!NOTE]
> このサービスは、通常は変更されません直接既定の実装が通常の ADO.NET プロバイダーの登録を使用しているためです。 EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System.Data.Entity.Infrastructure.IProviderInvariantName  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 特定の種類の DbProviderFactory のプロバイダーの不変名を判断するために使用するサービス。 このサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。 つまり、DbProviderFactory が EF によって解決されるため、ADO.NET プロバイダーでは通常の方法で登録されていない場合もあるこのサービスを解決するために必要です。  

**キー**: The DbProviderFactory インスタンスが不変の名前が必要です。  

>[!NOTE]
> EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。  

### <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 生成済みのビューを含むアセンブリのキャッシュ。 置換を ef に通知するアセンブリは、すべての検出を実行しなくても事前生成済みのビューを含めることが通常使用されます。  

**キー**: いない使用は、null になります  

### <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System.Data.Entity.Infrastructure.Pluralization.IPluralizationService

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: 複数形にして、名前を単数に EF によって使用されるサービスです。 既定では、英語版の複数形化サービスが使用されます。  

**キー**: いない使用は、null になります  

### <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System.Data.Entity.Infrastructure.Interception.IDbInterceptor  

**バージョンが導入された**: EF6.0.0

**返されるオブジェクト**: 任意のインターセプター アプリケーションの起動時に登録する必要があります。 GetServices 呼び出しを使用してこれらのオブジェクトが要求されると、任意の依存関係競合回避モジュールによって返されるすべてのインターセプターが登録されています。

**キー**: いない使用は、null になります。  

### <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func < System.Data.Entity.DbContext、アクション < 文字列\>、System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\>  

**バージョンが導入された**: EF6.0.0  

**返されるオブジェクト**: ファクトリとなるデータベース ログのフォーマッタを作成するために使用する際に使用されるコンテキスト。Database.Log プロパティは、指定されたコンテキストに設定されます。  

**キー**: いない使用は、null になります。  

### <a name="funcsystemdataentitydbcontext"></a>Func < System.Data.Entity.DbContext\>  

**バージョンが導入された**: EF6.1.0  

**返されるオブジェクト**: インスタンスを作成するコンテキストを移行するため、コンテキストにアクセス可能なパラメーターなしコンス トラクターがあるない場合に使用されるファクトリ。  

**キー**:: Type オブジェクト ファクトリを必要とする派生 DbContext の種類に対応します。  

### <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func < System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\>  

**バージョンが導入された**: EF6.1.0  

**返されるオブジェクト**: シリアル化および Code First Migrations で使用するための XML に desterilized ように厳密に型指定されたカスタム注釈をシリアル化するためのシリアライザーの作成に使用されるファクトリ。  

**キー**: されている注釈の名前をシリアル化または逆シリアル化します。  

### <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func < System.Data.Entity.Infrastructure.TransactionHandler\>  

**バージョンが導入された**: EF6.1.0  

**返されるオブジェクト**: コミット エラーを処理するなどの場合、特別な処理を適用できるように、トランザクションのハンドラーを作成するために使用されるファクトリ。  

**キー**: プロバイダーの不変名と必要に応じて、トランザクションのハンドラーを使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。  
