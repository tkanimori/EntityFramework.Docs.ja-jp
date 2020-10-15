---
title: 依存関係の解決-EF6
description: Entity Framework 6 の依存関係の解決
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 84f2391cdad974bb14aecd7e8650d46e78e47b75
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063310"
---
# <a name="dependency-resolution"></a><span data-ttu-id="62077-103">依存関係の解決</span><span class="sxs-lookup"><span data-stu-id="62077-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="62077-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="62077-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="62077-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="62077-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="62077-106">EF6 以降、Entity Framework には、必要なサービスの実装を取得するための汎用機構が含まれています。</span><span class="sxs-lookup"><span data-stu-id="62077-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="62077-107">つまり、EF が一部のインターフェイスまたは基本クラスのインスタンスを使用する場合、使用するインターフェイスまたは基本クラスの具象実装を要求します。</span><span class="sxs-lookup"><span data-stu-id="62077-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="62077-108">これは、IDbDependencyResolver インターフェイスを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="62077-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="62077-109">GetService メソッドは、通常 EF によって呼び出され、EF またはアプリケーションによって提供される IDbDependencyResolver の実装によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="62077-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="62077-110">型引数が呼び出されると、要求されているサービスのインターフェイスまたは基本クラスの型になります。キーオブジェクトは、null または要求されたサービスに関するコンテキスト情報を提供するオブジェクトのいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="62077-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="62077-111">特に指定がない限り、返されるオブジェクトはシングルトンとして使用できるため、スレッドセーフである必要があります。</span><span class="sxs-lookup"><span data-stu-id="62077-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="62077-112">多くの場合、返されるオブジェクトはファクトリであり、ファクトリ自体はスレッドセーフである必要がありますが、ファクトリから返されるオブジェクトは、使用するたびにファクトリから新しいインスタンスが要求されるため、スレッドセーフである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="62077-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="62077-113">この記事には、IDbDependencyResolver を実装する方法の詳細は含まれていませんが、代わりに、EF が GetService を呼び出すサービスの種類 (インターフェイスと基本クラスの型)、およびこれらの各呼び出しのキーオブジェクトのセマンティクスを参照として機能します。</span><span class="sxs-lookup"><span data-stu-id="62077-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="62077-114">Data. Entity. IDatabaseInitializer<TContext\></span><span class="sxs-lookup"><span data-stu-id="62077-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="62077-115">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-116">**返されたオブジェクト**: 指定されたコンテキスト型のデータベース初期化子</span><span class="sxs-lookup"><span data-stu-id="62077-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="62077-117">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="62077-118">Func は MigrationSqlGenerator を<します。\></span><span class="sxs-lookup"><span data-stu-id="62077-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="62077-119">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="62077-120">**返されるオブジェクト**: データベースを作成するために使用できる SQL ジェネレーターを作成するファクトリ (データベース初期化子を使用したデータベースの作成など)。</span><span class="sxs-lookup"><span data-stu-id="62077-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="62077-121">**Key**: SQL が生成されるデータベースの種類を指定する ADO.NET プロバイダーの不変名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="62077-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="62077-122">たとえば、キー "system.string" に対して SQL Server SQL ジェネレーターが返されます。</span><span class="sxs-lookup"><span data-stu-id="62077-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-123">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="62077-124">DbProviderServices.............</span><span class="sxs-lookup"><span data-stu-id="62077-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="62077-125">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-126">**返されたオブジェクト**: 指定したプロバイダーの不変名に使用する EF プロバイダー</span><span class="sxs-lookup"><span data-stu-id="62077-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="62077-127">**Key**: プロバイダーが必要なデータベースの種類を指定する ADO.NET プロバイダーの不変名を含む文字列。</span><span class="sxs-lookup"><span data-stu-id="62077-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="62077-128">たとえば、キー "system.string" に対して SQL Server プロバイダーが返されます。</span><span class="sxs-lookup"><span data-stu-id="62077-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-129">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="62077-130">IDbConnectionFactory (システムのデータ)</span><span class="sxs-lookup"><span data-stu-id="62077-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="62077-131">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-132">**返されるオブジェクト**: EF が規約に従ってデータベース接続を作成するときに使用される接続ファクトリ。</span><span class="sxs-lookup"><span data-stu-id="62077-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="62077-133">つまり、EF に接続文字列または接続文字列が指定されておらず、app.config または web.config に接続文字列が見つからない場合は、このサービスを使用して、規則に従って接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="62077-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="62077-134">接続ファクトリを変更すると、EF が既定で別の種類のデータベース (SQL Server Compact Edition など) を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="62077-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="62077-135">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-136">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="62077-137">IManifestTokenService (システムのデータ)</span><span class="sxs-lookup"><span data-stu-id="62077-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="62077-138">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-139">**返されるオブジェクト**: 接続からプロバイダーマニフェストトークンを生成できるサービス。</span><span class="sxs-lookup"><span data-stu-id="62077-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="62077-140">通常、このサービスは2つの方法で使用されます。</span><span class="sxs-lookup"><span data-stu-id="62077-140">This service is typically used in two ways.</span></span> <span data-ttu-id="62077-141">まず、モデルを構築するときにデータベースに接続する Code First を避けるために使用できます。</span><span class="sxs-lookup"><span data-stu-id="62077-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="62077-142">次に、特定のデータベースバージョンのモデルを強制的に Code First するために使用できます。たとえば、SQL Server 2008 が使用されている場合でも、SQL Server 2005 のモデルを強制的に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="62077-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="62077-143">**オブジェクトの有効期間**: シングルトン: 同じオブジェクトが複数回使用され、異なるスレッドによって同時に使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="62077-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="62077-144">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="62077-145">IDbProviderFactoryService (システムのデータ)</span><span class="sxs-lookup"><span data-stu-id="62077-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="62077-146">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-147">**返されたオブジェクト**: 特定の接続からプロバイダーファクトリを取得できるサービス。</span><span class="sxs-lookup"><span data-stu-id="62077-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="62077-148">.NET 4.5 では、プロバイダーは接続からパブリックにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="62077-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="62077-149">.NET 4 では、このサービスの既定の実装は、特定のヒューリスティックを使用して一致するプロバイダーを検索します。</span><span class="sxs-lookup"><span data-stu-id="62077-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="62077-150">これらのエラーが発生した場合は、適切な解決策を提供するために、このサービスの新しい実装を登録できます。</span><span class="sxs-lookup"><span data-stu-id="62077-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="62077-151">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="62077-152">Func<DbContext、IDbModelCacheKey になります。\></span><span class="sxs-lookup"><span data-stu-id="62077-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="62077-153">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-154">**返さ**れたオブジェクト: 特定のコンテキストのモデルキャッシュキーを生成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="62077-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="62077-155">既定では、EF は、プロバイダーごとに DbContext 型ごとに1つのモデルをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="62077-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="62077-156">このサービスの別の実装を使用して、スキーマ名などの他の情報をキャッシュキーに追加できます。</span><span class="sxs-lookup"><span data-stu-id="62077-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="62077-157">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="62077-158">DbSpatialServices (システムのデータ)</span><span class="sxs-lookup"><span data-stu-id="62077-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="62077-159">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-160">**返されるオブジェクト**: geography および geometry 空間型の基本 ef プロバイダーにサポートを追加する ef 空間プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="62077-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="62077-161">**キー**: dbsptialservices は2つの方法で要求されます。</span><span class="sxs-lookup"><span data-stu-id="62077-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="62077-162">最初に、キーとして DbProviderInfo オブジェクト (不変名とマニフェストトークンを含む) を使用して、プロバイダー固有の空間サービスが要求されます。</span><span class="sxs-lookup"><span data-stu-id="62077-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="62077-163">2つ目は、キーなしで DbSpatialServices を要求できることです。</span><span class="sxs-lookup"><span data-stu-id="62077-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="62077-164">これは、スタンドアロンの DbGeography 型または Dbgeography 型を作成するときに使用される "グローバル空間プロバイダー" を解決するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62077-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-165">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="62077-166">Func は IDbExecutionStrategy を<します。\></span><span class="sxs-lookup"><span data-stu-id="62077-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="62077-167">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-168">**返されるオブジェクト**: データベースに対してクエリとコマンドが実行されたときに、プロバイダーが再試行またはその他の動作を実装できるようにするサービスを作成するファクトリ。</span><span class="sxs-lookup"><span data-stu-id="62077-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="62077-169">実装が指定されていない場合、EF は単にコマンドを実行し、スローされた例外をすべて伝達します。</span><span class="sxs-lookup"><span data-stu-id="62077-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="62077-170">SQL Server は、このサービスを使用して再試行ポリシーを提供します。これは、SQL Azure などのクラウドベースのデータベースサーバーに対して実行する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="62077-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="62077-171">**Key**: プロバイダーの不変名と、必要に応じて実行戦略を使用するサーバー名を含む ExecutionStrategyKey オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="62077-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-172">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="62077-173">Func<DbConnection、string、または system.string です。履歴コンテキスト\></span><span class="sxs-lookup"><span data-stu-id="62077-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="62077-174">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-175">**返されるオブジェクト**: プロバイダーが、 `__MigrationHistory` EF の移行で使用されるテーブルへの履歴コンテキストのマッピングを構成できるようにするファクトリ。</span><span class="sxs-lookup"><span data-stu-id="62077-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="62077-176">履歴コンテキストは Code First DbContext であり、テーブルの名前や列マッピングの仕様などを変更するために通常の fluent API を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="62077-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="62077-177">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-178">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="62077-179">DbProviderFactory (システム)</span><span class="sxs-lookup"><span data-stu-id="62077-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="62077-180">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-181">**返されたオブジェクト**: 指定したプロバイダーの不変名に使用する ADO.NET プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="62077-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="62077-182">**Key**: ADO.NET プロバイダーの不変名を含む文字列</span><span class="sxs-lookup"><span data-stu-id="62077-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-183">既定の実装では通常の ADO.NET プロバイダー登録が使用されるため、このサービスは通常は直接変更されません。</span><span class="sxs-lookup"><span data-stu-id="62077-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="62077-184">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="62077-185">IProviderInvariantName (システムのデータ)</span><span class="sxs-lookup"><span data-stu-id="62077-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="62077-186">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-187">**返されたオブジェクト**: 特定の種類の DbProviderFactory に対してプロバイダーの不変名を決定するために使用されるサービス。</span><span class="sxs-lookup"><span data-stu-id="62077-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="62077-188">このサービスの既定の実装では、ADO.NET プロバイダーの登録が使用されます。</span><span class="sxs-lookup"><span data-stu-id="62077-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="62077-189">これは、DbProviderFactory が EF によって解決されているために ADO.NET プロバイダーが通常の方法で登録されていない場合にも、このサービスを解決する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="62077-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="62077-190">**キー**: 不変名を必要とする DbProviderFactory インスタンス。</span><span class="sxs-lookup"><span data-stu-id="62077-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="62077-191">EF6 のプロバイダー関連サービスの詳細については、 [EF6 プロバイダーモデル](xref:ef6/fundamentals/providers/provider-model) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="62077-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="62077-192">System.string...............................</span><span class="sxs-lookup"><span data-stu-id="62077-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="62077-193">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-194">**返されたオブジェクト**: 事前に生成されたビューを含むアセンブリのキャッシュ。</span><span class="sxs-lookup"><span data-stu-id="62077-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="62077-195">置換は通常、検出を行わずに、事前に生成されたビューを含むアセンブリを EF に知らせるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="62077-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="62077-196">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="62077-197">複数形化. IPluralizationService (その他)</span><span class="sxs-lookup"><span data-stu-id="62077-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="62077-198">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-199">**返されたオブジェクト**: EF によって複数化と単数の名前に使用されるサービス。</span><span class="sxs-lookup"><span data-stu-id="62077-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="62077-200">既定では、英語の複数形化サービスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="62077-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="62077-201">**キー**: 使用されていません。null になります</span><span class="sxs-lookup"><span data-stu-id="62077-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="62077-202">IDbInterceptor のようになります。</span><span class="sxs-lookup"><span data-stu-id="62077-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="62077-203">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="62077-204">**返されるオブジェクト**: アプリケーションの起動時に登録する必要があるインターセプター。</span><span class="sxs-lookup"><span data-stu-id="62077-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="62077-205">これらのオブジェクトは GetServices 呼び出しを使用して要求され、依存関係競合回避モジュールによって返されるすべてのインターセプターが登録されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="62077-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="62077-206">**キー**: 使用されていません。null になります。</span><span class="sxs-lookup"><span data-stu-id="62077-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="62077-207">Func<system.object。 DbContext、Action<string、system.string、. \> DatabaseLogFormatter ですが、この関数は\></span><span class="sxs-lookup"><span data-stu-id="62077-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="62077-208">**導入**されたバージョン: ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="62077-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="62077-209">**返されるオブジェクト**: コンテキストで使用されるデータベースログフォーマッタを作成するために使用されるファクトリ。指定されたコンテキストに対してデータベース .Log プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="62077-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="62077-210">**キー**: 使用されていません。null になります。</span><span class="sxs-lookup"><span data-stu-id="62077-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="62077-211">Func<system.object の DbContext\></span><span class="sxs-lookup"><span data-stu-id="62077-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="62077-212">**導入**されたバージョン: ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="62077-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="62077-213">**返されるオブジェクト**: コンテキストにアクセス可能なパラメーターなしのコンストラクターがない場合に、移行用のコンテキストインスタンスを作成するために使用されるファクトリ。</span><span class="sxs-lookup"><span data-stu-id="62077-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="62077-214">**Key**: ファクトリが必要な派生 dbcontext の型の型オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="62077-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="62077-215">Func は IMetadataAnnotationSerializer を<しています。\></span><span class="sxs-lookup"><span data-stu-id="62077-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="62077-216">**導入**されたバージョン: ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="62077-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="62077-217">**返されたオブジェクト**: 厳密に型指定されたカスタム注釈をシリアル化するためにシリアライザーを作成するために使用されるファクトリ。 Code First Migrations で使用するために、XML にシリアル化して destを認識することができます。</span><span class="sxs-lookup"><span data-stu-id="62077-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="62077-218">**キー**: シリアル化または逆シリアル化される注釈の名前。</span><span class="sxs-lookup"><span data-stu-id="62077-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="62077-219">Func<system.object... Entity. Infrastructure\></span><span class="sxs-lookup"><span data-stu-id="62077-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="62077-220">**導入**されたバージョン: ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="62077-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="62077-221">**返されるオブジェクト**: トランザクションのハンドラーを作成するために使用されるファクトリ。これにより、コミットエラーの処理などの状況に対して特別な処理を適用できます。</span><span class="sxs-lookup"><span data-stu-id="62077-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="62077-222">**Key**: ExecutionStrategyKey オブジェクト。プロバイダーの不変名と、必要に応じて、トランザクションハンドラーを使用するサーバー名を格納します。</span><span class="sxs-lookup"><span data-stu-id="62077-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
