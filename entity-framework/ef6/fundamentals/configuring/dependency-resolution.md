---
title: 依存関係の解決 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
caps.latest.revision: 3
ms.openlocfilehash: 88fd859b4f9a8069eeb08f32bb1d35ddcd21aec5
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122789"
---
# <a name="dependency-resolution"></a><span data-ttu-id="ba4b0-102">依存関係の解決</span><span class="sxs-lookup"><span data-stu-id="ba4b0-102">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="ba4b0-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ba4b0-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ba4b0-105">EF6 から始めて、Entity Framework には、必要なサービスの実装を取得するための汎用的なメカニズムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-105">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="ba4b0-106">つまり、EF がいくつかのインターフェイスまたは基本クラスのインスタンスを使用する場合たずねられますインターフェイスまたは基本クラスの具象実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-106">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="ba4b0-107">これは、IDbDependencyResolver インターフェイスを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-107">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="ba4b0-108">GetService メソッドは、EF によって呼び出されます、IDbDependencyResolver EF またはアプリケーションのいずれかを指定の実装によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-108">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="ba4b0-109">型引数が要求されているサービスのインターフェイスまたは基本クラス型で呼び出されると、およびキー オブジェクトが null または要求されたサービスに関するコンテキスト情報を提供するオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-109">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="ba4b0-110">この記事では IDbDependencyResolver を実装する方法の完全な詳細情報が含まれていないが、代わりにする EF の呼び出し GetService とキーのオブジェクトのセマンティクスのこれらの各サービスの種類 (つまり、インターフェイスおよび基本クラス型) への参照として機能します呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-110">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span> <span data-ttu-id="ba4b0-111">このドキュメントは最新の他のサービスが追加されるとします。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-111">This document will be kept up-to-date as additional services are added.</span></span>  

## <a name="services-resolved"></a><span data-ttu-id="ba4b0-112">サービスの解決</span><span class="sxs-lookup"><span data-stu-id="ba4b0-112">Services resolved</span></span>  

<span data-ttu-id="ba4b0-113">限りをシングルトンとして使用できるため、返される任意のオブジェクトはスレッド セーフなをする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-113">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="ba4b0-114">多くの場合、ファクトリをこの例では、オブジェクトが返されます factory 自体はスレッド セーフである必要がありますが、ファクトリから返されるオブジェクトが使用するたびにファクトリから新しいインスタンスが要求されるので、スレッド セーフである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-114">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>  

### <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="ba4b0-115">System.Data.Entity.IDatabaseInitializer < TContext\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-115">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="ba4b0-116">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-116">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-117">**返されるオブジェクト**: 特定のコンテキストの種類のデータベース初期化子</span><span class="sxs-lookup"><span data-stu-id="ba4b0-117">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="ba4b0-118">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-118">**Key**: Not used; will be null</span></span>  

### <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="ba4b0-119">Func < System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-119">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="ba4b0-120">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-120">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="ba4b0-121">**返されるオブジェクト**: 移行とデータベース初期化子を含むデータベースの作成など、作成するデータベースを原因となるその他の操作に使用できる SQL ジェネレーターを作成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-121">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="ba4b0-122">**キー**: SQL の生成対象となるデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-122">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="ba4b0-123">たとえば、キー"System.Data.SqlClient"の SQL Server SQL ジェネレーターが返されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-123">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-124">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-124">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="ba4b0-125">System.Data.Entity.Core.Common.DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="ba4b0-125">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="ba4b0-126">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-126">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-127">**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する EF プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ba4b0-127">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="ba4b0-128">**キー**: プロバイダーを必要とするデータベースの種類を指定する ADO.NET プロバイダー固定名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-128">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="ba4b0-129">たとえば、キー"System.Data.SqlClient"の SQL Server プロバイダーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-129">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-130">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-130">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="ba4b0-131">System.Data.Entity.Infrastructure.IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="ba4b0-131">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="ba4b0-132">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-132">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-133">**返されるオブジェクト**: 慣例 EF がデータベース接続を作成するときに使用する接続ファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-133">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="ba4b0-134">つまり、ef では、接続または接続文字列を指定しないと、app.config または web.config 接続文字列が見つかりません、し、このサービスは使用接続を作成する慣例します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-134">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="ba4b0-135">接続ファクトリを変更すると、既定では、さまざまな種類のデータベース (たとえば、SQL Server Compact Edition) を使用する EF を許可できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-135">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="ba4b0-136">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-136">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-137">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-137">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="ba4b0-138">System.Data.Entity.Infrastructure.IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="ba4b0-138">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="ba4b0-139">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-139">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-140">**返されるオブジェクト**: 接続から、プロバイダー マニフェスト トークンを生成できるサービスです。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-140">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="ba4b0-141">このサービスは通常、2 つの方法で使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-141">This service is typically used in two ways.</span></span> <span data-ttu-id="ba4b0-142">最初に、Code First モデルを構築するときに、データベースへの接続を回避するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-142">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="ba4b0-143">次に、Code First 場合でも、SQL Server 2008 を使用することがあります、SQL Server 2005 のモデルを強制する - たとえば、特定のデータベース バージョンのモデルの構築に強制的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-143">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="ba4b0-144">**オブジェクトの有効期間**: シングルトン - 同じオブジェクトは時間と別のスレッドで同時に使用される複数である可能性があります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-144">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="ba4b0-145">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-145">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="ba4b0-146">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="ba4b0-146">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="ba4b0-147">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-147">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-148">**返されるオブジェクト**: サービスを特定の接続からプロバイダー ファクトリを取得できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-148">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="ba4b0-149">.NET 4.5 では、プロバイダーは、接続からパブリックにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-149">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="ba4b0-150">このサービスの既定の実装では、.NET 4 で、一部のヒューリスティックを使用して、一致するプロバイダーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-150">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="ba4b0-151">適切な解決を提供する場合、これらが失敗し、このサービスの新しい実装を登録できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-151">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="ba4b0-152">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-152">**Key**: Not used; will be null</span></span>  

### <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="ba4b0-153">Func < DbContext、System.Data.Entity.Infrastructure.IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-153">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="ba4b0-154">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-154">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-155">**返されるオブジェクト**: 指定されたコンテキストのモデルのキャッシュ キーを生成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-155">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="ba4b0-156">既定では、EF は、プロバイダーごとに DbContext 型ごとに 1 つのモデルをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-156">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="ba4b0-157">キャッシュ キーにスキーマ名などの他の情報を追加する、このサービスの別の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-157">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="ba4b0-158">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-158">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="ba4b0-159">System.Data.Entity.Spatial.DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="ba4b0-159">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="ba4b0-160">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-160">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-161">**返されるオブジェクト**: geography および geometry 空間型の基本的な EF プロバイダーにサポートを追加する EF の空間プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-161">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="ba4b0-162">**キー**: DbSptialServices は 2 つの方法でのように求められます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-162">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="ba4b0-163">DbProviderInfo オブジェクトを使用して最初に、プロバイダー固有の空間サービスが要求された (不変式が含まれていますとマニフェスト トークン) をキーとして。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-163">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="ba4b0-164">第 2 に、DbSpatialServices 要求できるキーを持たない。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-164">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="ba4b0-165">これは、解決するには、"グローバル空間 provider"DbGeography または DbGeometry のスタンドアロン型の作成時に使用するに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-165">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-166">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-166">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="ba4b0-167">Func < System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-167">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="ba4b0-168">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-168">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-169">**返されるオブジェクト**: サービスにより、クエリとコマンドは、データベースに対して実行されたときに再試行する場合やその他の動作を実装するプロバイダーを作成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-169">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="ba4b0-170">実装が指定されていない場合 EF は単にコマンドを実行し、スローされた例外を伝達します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-170">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="ba4b0-171">SQL Server のこのサービスは、SQL Azure などのクラウド ベースのデータベース サーバーに対して実行したときに特に便利ですが、再試行ポリシーを提供する使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-171">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="ba4b0-172">**キー**: プロバイダーの不変名と必要に応じて、実行方法を使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-172">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-173">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-173">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="ba4b0-174">Func < DbConnection、文字列、System.Data.Entity.Migrations.History.HistoryContext\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-174">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="ba4b0-175">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-175">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-176">**返されるオブジェクト**: プロバイダーを HistoryContext のマッピングを構成できるようにするファクトリ、 `__MigrationHistory` EF の移行で使用されるテーブル。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-176">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="ba4b0-177">HistoryContext コードの最初の DbContext であり、名前のテーブルと列マッピングの指定などを変更する通常の fluent API を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-177">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="ba4b0-178">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-178">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-179">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-179">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="ba4b0-180">System.Data.Common.DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="ba4b0-180">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="ba4b0-181">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-181">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-182">**返されるオブジェクト**: 指定したプロバイダーの不変名を使用する ADO.NET プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-182">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="ba4b0-183">**キー**: ADO.NET プロバイダーの不変名を含む文字列</span><span class="sxs-lookup"><span data-stu-id="ba4b0-183">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-184">このサービスは、通常は変更されません直接既定の実装が通常の ADO.NET プロバイダーの登録を使用しているためです。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-184">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="ba4b0-185">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-185">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="ba4b0-186">System.Data.Entity.Infrastructure.IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="ba4b0-186">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="ba4b0-187">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-187">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-188">**返されるオブジェクト**: 特定の種類の DbProviderFactory のプロバイダーの不変名を判断するために使用するサービス。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-188">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="ba4b0-189">このサービスの既定の実装では、ADO.NET プロバイダーの登録を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-189">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="ba4b0-190">つまり、DbProviderFactory が EF によって解決されるため、ADO.NET プロバイダーでは通常の方法で登録されていない場合もあるこのサービスを解決するために必要です。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-190">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="ba4b0-191">**キー**: The DbProviderFactory インスタンスが不変の名前が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-191">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="ba4b0-192">EF6 でプロバイダーに関連するサービスの詳細についてを参照してください、 [EF6 プロバイダー モデル](~/ef6/fundamentals/providers/provider-model.md)セクション。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-192">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="ba4b0-193">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="ba4b0-193">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="ba4b0-194">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-194">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-195">**返されるオブジェクト**: 生成済みのビューを含むアセンブリのキャッシュ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-195">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="ba4b0-196">置換を ef に通知するアセンブリは、すべての検出を実行しなくても事前生成済みのビューを含めることが通常使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-196">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="ba4b0-197">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-197">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="ba4b0-198">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="ba4b0-198">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="ba4b0-199">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-199">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-200">**返されるオブジェクト**: 複数形にして、名前を単数に EF によって使用されるサービスです。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-200">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="ba4b0-201">既定では、英語版の複数形化サービスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-201">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="ba4b0-202">**キー**: いない使用は、null になります</span><span class="sxs-lookup"><span data-stu-id="ba4b0-202">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="ba4b0-203">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="ba4b0-203">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="ba4b0-204">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-204">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="ba4b0-205">**返されるオブジェクト**: 任意のインターセプター アプリケーションの起動時に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-205">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="ba4b0-206">GetServices 呼び出しを使用してこれらのオブジェクトが要求されると、任意の依存関係競合回避モジュールによって返されるすべてのインターセプターが登録されています。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-206">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="ba4b0-207">**キー**: いない使用は、null になります。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-207">**Key**: Not used; will be null.</span></span>  

### <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="ba4b0-208">Func < System.Data.Entity.DbContext、アクション < 文字列\>、System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-208">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="ba4b0-209">**バージョンが導入された**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-209">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="ba4b0-210">**返されるオブジェクト**: ファクトリとなるデータベース ログのフォーマッタを作成するために使用する際に使用されるコンテキスト。Database.Log プロパティは、指定されたコンテキストに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-210">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="ba4b0-211">**キー**: いない使用は、null になります。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-211">**Key**: Not used; will be null.</span></span>  

### <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="ba4b0-212">Func < System.Data.Entity.DbContext\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-212">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="ba4b0-213">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-213">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="ba4b0-214">**返されるオブジェクト**: インスタンスを作成するコンテキストを移行するため、コンテキストにアクセス可能なパラメーターなしコンス トラクターがあるない場合に使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-214">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="ba4b0-215">**キー**:: Type オブジェクト ファクトリを必要とする派生 DbContext の種類に対応します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-215">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

### <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="ba4b0-216">Func < System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-216">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="ba4b0-217">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-217">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="ba4b0-218">**返されるオブジェクト**: シリアル化および Code First Migrations で使用するための XML に desterilized ように厳密に型指定されたカスタム注釈をシリアル化するためのシリアライザーの作成に使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-218">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="ba4b0-219">**キー**: されている注釈の名前をシリアル化または逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-219">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

### <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="ba4b0-220">Func < System.Data.Entity.Infrastructure.TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="ba4b0-220">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="ba4b0-221">**バージョンが導入された**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="ba4b0-221">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="ba4b0-222">**返されるオブジェクト**: コミット エラーを処理するなどの場合、特別な処理を適用できるように、トランザクションのハンドラーを作成するために使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-222">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="ba4b0-223">**キー**: プロバイダーの不変名と必要に応じて、トランザクションのハンドラーを使用するサーバー名を含む、ExecutionStrategyKey オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="ba4b0-223">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
