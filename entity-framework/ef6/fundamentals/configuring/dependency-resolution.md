---
title: 依存関係の解決 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
ms.openlocfilehash: c6c56c3048e17a5c888ffe564e7606abf8b0c4ed
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251246"
---
# <a name="dependency-resolution"></a><span data-ttu-id="bd8ec-102">依存関係の解決</span><span class="sxs-lookup"><span data-stu-id="bd8ec-102">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="bd8ec-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="bd8ec-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="bd8ec-105">EF6 から始めて、Entity Framework には、必要なサービスの実装を取得するための汎用的なメカニズムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-105">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="bd8ec-106">つまり、EF がいくつかのインターフェイスまたは基本クラスのインスタンスを使用する場合たずねられますインターフェイスまたは基本クラスの具象実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-106">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="bd8ec-107">これは、IDbDependencyResolver インターフェイスを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-107">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="bd8ec-108">GetService メソッドは、EF によって呼び出されます、IDbDependencyResolver EF またはアプリケーションのいずれかを指定の実装によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-108">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="bd8ec-109">型引数が要求されているサービスのインターフェイスまたは基本クラス型で呼び出されると、およびキー オブジェクトが null または要求されたサービスに関するコンテキスト情報を提供するオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-109">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="bd8ec-110">限りをシングルトンとして使用できるため、返される任意のオブジェクトはスレッド セーフなをする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-110">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="bd8ec-111">多くの場合、ファクトリをこの例では、オブジェクトが返されます factory 自体はスレッド セーフである必要がありますが、ファクトリから返されるオブジェクトが使用するたびにファクトリから新しいインスタンスが要求されるので、スレッド セーフである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-111">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="bd8ec-112">この記事では IDbDependencyResolver を実装する方法の完全な詳細情報が含まれていないが、代わりにする EF の呼び出し GetService とキーのオブジェクトのセマンティクスのこれらの各サービスの種類 (つまり、インターフェイスおよび基本クラス型) への参照として機能します呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-112">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="bd8ec-113">System.Data.Entity.IDatabaseInitializer < TContext\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-113">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="bd8ec-114">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-114">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-115">**返されるオブジェクト**: 特定のコンテキストの種類のデータベース初期化子</span><span class="sxs-lookup"><span data-stu-id="bd8ec-115">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="bd8ec-116">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-116">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="bd8ec-117">Func < System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-117">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="bd8ec-118">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-118">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="bd8ec-119">**返されるオブジェクト**: 移行とデータベース初期化子を含むデータベースの作成など、作成するデータベースを原因となるその他の操作に使用できる SQL ジェネレーターを作成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-119">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="bd8ec-120">**キー**: SQL の生成対象となるデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-120">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="bd8ec-121">たとえば、キー"System.Data.SqlClient"の SQL Server SQL ジェネレーターが返されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-121">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-122">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-122">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="bd8ec-123">System.Data.Entity.Core.Common.DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="bd8ec-123">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="bd8ec-124">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-124">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-125">**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する EF プロバイダー</span><span class="sxs-lookup"><span data-stu-id="bd8ec-125">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="bd8ec-126">**キー**: プロバイダーを必要とするデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-126">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="bd8ec-127">たとえば、キー"System.Data.SqlClient"の SQL Server プロバイダーが返されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-127">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-128">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-128">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="bd8ec-129">System.Data.Entity.Infrastructure.IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="bd8ec-129">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="bd8ec-130">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-130">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-131">**返されるオブジェクト**: 慣例 EF がデータベース接続を作成するときに使用する接続ファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-131">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="bd8ec-132">つまり、ef では、接続または接続文字列を指定しないと、app.config または web.config 接続文字列が見つかりません、し、このサービスは使用接続を作成する慣例します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-132">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="bd8ec-133">接続ファクトリを変更すると、既定では、さまざまな種類のデータベース (たとえば、SQL Server Compact Edition) を使用する EF を許可できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-133">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="bd8ec-134">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-134">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-135">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-135">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="bd8ec-136">System.Data.Entity.Infrastructure.IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="bd8ec-136">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="bd8ec-137">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-137">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-138">**返されるオブジェクト**: 接続から、プロバイダー マニフェスト トークンを生成できるサービスです。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-138">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="bd8ec-139">このサービスは通常、2 つの方法で使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-139">This service is typically used in two ways.</span></span> <span data-ttu-id="bd8ec-140">最初に、Code First モデルを構築するときに、データベースへの接続を回避するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-140">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="bd8ec-141">次に、Code First 場合でも、SQL Server 2008 を使用することがあります、SQL Server 2005 のモデルを強制する - たとえば、特定のデータベース バージョンのモデルの構築に強制的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-141">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="bd8ec-142">**オブジェクトの有効期間**: シングルトン - 同じオブジェクトは時間と別のスレッドで同時に使用される複数である可能性があります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-142">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="bd8ec-143">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-143">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="bd8ec-144">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="bd8ec-144">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="bd8ec-145">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-145">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-146">**返されるオブジェクト**: サービスを特定の接続からプロバイダー ファクトリを取得できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-146">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="bd8ec-147">.NET 4.5 では、プロバイダーは、接続からパブリックにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-147">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="bd8ec-148">このサービスの既定の実装では、.NET 4 で、一部のヒューリスティックを使用して、一致するプロバイダーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-148">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="bd8ec-149">適切な解決を提供する場合、これらが失敗し、このサービスの新しい実装を登録できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-149">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="bd8ec-150">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-150">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="bd8ec-151">Func < DbContext、System.Data.Entity.Infrastructure.IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-151">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="bd8ec-152">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-152">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-153">**返されるオブジェクト**: 指定されたコンテキストのモデルのキャッシュ キーを生成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-153">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="bd8ec-154">既定では、EF は、プロバイダーごとに DbContext 型ごとに 1 つのモデルをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-154">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="bd8ec-155">キャッシュ キーにスキーマ名などの他の情報を追加する、このサービスの別の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-155">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="bd8ec-156">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-156">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="bd8ec-157">System.Data.Entity.Spatial.DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="bd8ec-157">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="bd8ec-158">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-158">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-159">**返されるオブジェクト**: geography および geometry 空間型の基本的な EF プロバイダーにサポートを追加する EF の空間プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-159">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="bd8ec-160">**キー**: DbSptialServices は 2 つの方法でのように求められます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-160">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="bd8ec-161">DbProviderInfo オブジェクトを使用して最初に、プロバイダー固有の空間サービスが要求された (不変式が含まれていますとマニフェスト トークン) をキーとして。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-161">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="bd8ec-162">第 2 に、DbSpatialServices 要求できるキーを持たない。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-162">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="bd8ec-163">これは、解決するには、"グローバル空間 provider"DbGeography または DbGeometry のスタンドアロン型の作成時に使用するに使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-163">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-164">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-164">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="bd8ec-165">Func < System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-165">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="bd8ec-166">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-166">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-167">**返されるオブジェクト**: サービスにより、クエリとコマンドは、データベースに対して実行されたときに再試行する場合やその他の動作を実装するプロバイダーを作成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-167">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="bd8ec-168">実装が指定されていない場合 EF は単にコマンドを実行し、スローされた例外を伝達します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-168">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="bd8ec-169">SQL Server のこのサービスは、SQL Azure などのクラウド ベースのデータベース サーバーに対して実行したときに特に便利ですが、再試行ポリシーを提供する使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-169">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="bd8ec-170">**キー**: プロバイダーの不変名と必要に応じて、実行方法を使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-170">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-171">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-171">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="bd8ec-172">Func < DbConnection、文字列、System.Data.Entity.Migrations.History.HistoryContext\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-172">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="bd8ec-173">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-173">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-174">**返されるオブジェクト**: プロバイダーを HistoryContext のマッピングを構成できるようにするファクトリ、 `__MigrationHistory` EF の移行で使用されるテーブル。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-174">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="bd8ec-175">HistoryContext コードの最初の DbContext であり、名前のテーブルと列マッピングの指定などを変更する通常の fluent API を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-175">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="bd8ec-176">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-176">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-177">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-177">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="bd8ec-178">System.Data.Common.DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="bd8ec-178">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="bd8ec-179">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-179">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-180">**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する ADO.NET プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-180">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="bd8ec-181">**キー**: ADO.NET プロバイダーの不変名を含む文字列</span><span class="sxs-lookup"><span data-stu-id="bd8ec-181">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-182">このサービスは、通常は変更されません直接既定の実装が通常の ADO.NET プロバイダーの登録を使用しているためです。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-182">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="bd8ec-183">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-183">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="bd8ec-184">System.Data.Entity.Infrastructure.IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="bd8ec-184">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="bd8ec-185">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-185">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-186">**返されるオブジェクト**: 特定の種類の DbProviderFactory のプロバイダーの不変名を判断するために使用するサービス。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-186">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="bd8ec-187">このサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-187">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="bd8ec-188">つまり、DbProviderFactory が EF によって解決されるため、ADO.NET プロバイダーでは通常の方法で登録されていない場合もあるこのサービスを解決するために必要です。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-188">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="bd8ec-189">**キー**: The DbProviderFactory インスタンスが不変の名前が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-189">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="bd8ec-190">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-190">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="bd8ec-191">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="bd8ec-191">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="bd8ec-192">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-192">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-193">**返されるオブジェクト**: 生成済みのビューを含むアセンブリのキャッシュ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-193">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="bd8ec-194">置換を ef に通知するアセンブリは、すべての検出を実行しなくても事前生成済みのビューを含めることが通常使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-194">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="bd8ec-195">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-195">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="bd8ec-196">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="bd8ec-196">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="bd8ec-197">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-197">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-198">**返されるオブジェクト**: 複数形にして、名前を単数に EF によって使用されるサービスです。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-198">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="bd8ec-199">既定では、英語版の複数形化サービスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-199">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="bd8ec-200">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="bd8ec-200">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="bd8ec-201">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="bd8ec-201">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="bd8ec-202">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-202">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="bd8ec-203">**返されるオブジェクト**: 任意のインターセプター アプリケーションの起動時に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-203">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="bd8ec-204">GetServices 呼び出しを使用してこれらのオブジェクトが要求されると、任意の依存関係競合回避モジュールによって返されるすべてのインターセプターが登録されています。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-204">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="bd8ec-205">**キー**: いない使用は、null になります。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-205">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="bd8ec-206">Func < System.Data.Entity.DbContext、アクション < 文字列\>、System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-206">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="bd8ec-207">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-207">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="bd8ec-208">**返されるオブジェクト**: ファクトリとなるデータベース ログのフォーマッタを作成するために使用する際に使用されるコンテキスト。Database.Log プロパティは、指定されたコンテキストに設定されます。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-208">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="bd8ec-209">**キー**: いない使用は、null になります。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-209">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="bd8ec-210">Func < System.Data.Entity.DbContext\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-210">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="bd8ec-211">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-211">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="bd8ec-212">**返されるオブジェクト**: インスタンスを作成するコンテキストを移行するため、コンテキストにアクセス可能なパラメーターなしコンス トラクターがあるない場合に使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-212">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="bd8ec-213">**キー**:: Type オブジェクト ファクトリを必要とする派生 DbContext の種類に対応します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-213">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="bd8ec-214">Func < System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-214">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="bd8ec-215">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-215">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="bd8ec-216">**返されるオブジェクト**: シリアル化および Code First Migrations で使用するための XML に desterilized ように厳密に型指定されたカスタム注釈をシリアル化するためのシリアライザーの作成に使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-216">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="bd8ec-217">**キー**: されている注釈の名前をシリアル化または逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-217">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="bd8ec-218">Func < System.Data.Entity.Infrastructure.TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="bd8ec-218">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="bd8ec-219">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="bd8ec-219">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="bd8ec-220">**返されるオブジェクト**: コミット エラーを処理するなどの場合、特別な処理を適用できるように、トランザクションのハンドラーを作成するために使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-220">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="bd8ec-221">**キー**: プロバイダーの不変名と必要に応じて、トランザクションのハンドラーを使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="bd8ec-221">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
