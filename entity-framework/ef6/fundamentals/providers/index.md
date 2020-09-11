---
title: Entity Framework プロバイダー - EF6
description: Entity Framework 6 の Entity Framework プロバイダー
author: divega
ms.date: 06/27/2018
ms.assetid: 7BFB7763-CD6C-4520-93A2-7B265F5FA586
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: 5140fefeb118b6e70ac27ab010d813d03ec7000c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615869"
---
# <a name="entity-framework-6-providers"></a><span data-ttu-id="854cd-103">Entity Framework 6 プロバイダー</span><span class="sxs-lookup"><span data-stu-id="854cd-103">Entity Framework 6 Providers</span></span>
> [!NOTE]
> <span data-ttu-id="854cd-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="854cd-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="854cd-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="854cd-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="854cd-106">Entity Framework は現在、オープンソース ライセンスで開発されており、EF6 以上は、.NET Framework の一部として出荷されません。</span><span class="sxs-lookup"><span data-stu-id="854cd-106">The Entity Framework is now being developed under an open-source license and EF6 and above will not be shipped as part of the .NET Framework.</span></span> <span data-ttu-id="854cd-107">これには多くの利点がありますが、EF のプロバイダーを EF6 アセンブリに対して再構築する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="854cd-107">This has many advantages but also requires that EF providers be rebuilt against the EF6 assemblies.</span></span> <span data-ttu-id="854cd-108">これは、EF5 以下用の EF プロバイダーは、再構築されるまで EF6 で動作しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="854cd-108">This means that EF providers for EF5 and below will not work with EF6 until they have been rebuilt.</span></span>

## <a name="which-providers-are-available-for-ef6"></a><span data-ttu-id="854cd-109">EF6 ではどのプロバイダーを使用できますか</span><span class="sxs-lookup"><span data-stu-id="854cd-109">Which providers are available for EF6?</span></span>

<span data-ttu-id="854cd-110">以下のプロバイダーは、EF6 用に再構築されていることを認識されています。</span><span class="sxs-lookup"><span data-stu-id="854cd-110">Providers we are aware of that have been rebuilt for EF6 include:</span></span>

*   <span data-ttu-id="854cd-111">**Microsoft SQL Server プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-111">**Microsoft SQL Server provider**</span></span>
    *   <span data-ttu-id="854cd-112">[Entity Framework オープン ソース コード ベース](https://github.com/aspnet/EntityFramework6)から構築されました。</span><span class="sxs-lookup"><span data-stu-id="854cd-112">Built from the [Entity Framework open source code base](https://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="854cd-113">[EntityFramework NuGet パッケージ](https://nuget.org/packages/EntityFramework)の一部として出荷されます。</span><span class="sxs-lookup"><span data-stu-id="854cd-113">Shipped as part of the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework)</span></span>
*   <span data-ttu-id="854cd-114">**Microsoft SQL Server Compact Edition プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-114">**Microsoft SQL Server Compact Edition provider**</span></span>
    *   <span data-ttu-id="854cd-115">[Entity Framework オープン ソース コード ベース](https://github.com/aspnet/EntityFramework6)から構築されました。</span><span class="sxs-lookup"><span data-stu-id="854cd-115">Built from the [Entity Framework open source code base](https://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="854cd-116">[EntityFramework.SqlServerCompact NuGet パッケージ](https://nuget.org/packages/EntityFramework.SqlServerCompact)に付属しています。</span><span class="sxs-lookup"><span data-stu-id="854cd-116">Shipped in the [EntityFramework.SqlServerCompact NuGet package](https://nuget.org/packages/EntityFramework.SqlServerCompact)</span></span>
*   [<span data-ttu-id="854cd-117">**Devart dotConnect データ プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-117">**Devart dotConnect Data Providers**</span></span>](https://www.devart.com/dotconnect/)
    *   <span data-ttu-id="854cd-118">Oracle、MySQL、PostgreSQL、SQLite、Salesforce、DB2、SQL Server などのを含むさまざまなデータベース用のサードパーティ プロバイダーが [Devart](https://www.devart.com/) から提供されています。</span><span class="sxs-lookup"><span data-stu-id="854cd-118">There are third-party providers from [Devart](https://www.devart.com/) for a variety of databases including Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2, and SQL Server</span></span>
*   [<span data-ttu-id="854cd-119">**CData Software プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-119">**CData Software providers**</span></span>](https://www.cdata.com/ado/)
    *   <span data-ttu-id="854cd-120">Salesforce、Azure Table Storage、MySql、その他多くのさまざまなデータ ストア用のサードパーティ プロバイダーが [CData Software](https://www.cdata.com/ado/) から提供されています。</span><span class="sxs-lookup"><span data-stu-id="854cd-120">There are third-party providers from [CData Software](https://www.cdata.com/ado/) for a variety of data stores including Salesforce, Azure Table Storage, MySql, and many more</span></span>
*   <span data-ttu-id="854cd-121">**Firebird プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-121">**Firebird provider**</span></span>
    *   <span data-ttu-id="854cd-122">[NuGet パッケージ](https://www.nuget.org/packages/EntityFramework.Firebird/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-122">Available as a [NuGet Package](https://www.nuget.org/packages/EntityFramework.Firebird/)</span></span>
*   <span data-ttu-id="854cd-123">**Visual Fox Pro プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="854cd-123">**Visual Fox Pro provider**</span></span>
    *   <span data-ttu-id="854cd-124">[NuGet パッケージ](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-124">Available as a [NuGet package](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)</span></span>
*   <span data-ttu-id="854cd-125">**MySQL**</span><span class="sxs-lookup"><span data-stu-id="854cd-125">**MySQL**</span></span>
    *   [<span data-ttu-id="854cd-126">Entity Framework の MySQL コネクタ/NET</span><span class="sxs-lookup"><span data-stu-id="854cd-126">MySQL Connector/NET for Entity Framework</span></span>](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   <span data-ttu-id="854cd-127">**PostgreSQL**</span><span class="sxs-lookup"><span data-stu-id="854cd-127">**PostgreSQL**</span></span>
    *   <span data-ttu-id="854cd-128">Npgsql は [NuGet パッケージ](https://www.nuget.org/packages/EntityFramework6.Npgsql/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-128">Npgsql is available as a [NuGet package](https://www.nuget.org/packages/EntityFramework6.Npgsql/)</span></span>
*   <span data-ttu-id="854cd-129">**Oracle**</span><span class="sxs-lookup"><span data-stu-id="854cd-129">**Oracle**</span></span>
    *   <span data-ttu-id="854cd-130">ODP.NET は [NuGet パッケージ](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-130">ODP.NET is available as a [NuGet package](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)</span></span>

<span data-ttu-id="854cd-131">この一覧に含まれていることは、EF6 用のビルドが使用可能になっていることのみを示し、特定のプロバイダーの機能またはサポートのレベルを示しているわけではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="854cd-131">Note that inclusion in this list does not indicate the level of functionality or support for a given provider, only that a build for EF6 has been made available.</span></span>

## <a name="registering-ef-providers"></a><span data-ttu-id="854cd-132">EF プロバイダーの登録</span><span class="sxs-lookup"><span data-stu-id="854cd-132">Registering EF providers</span></span>

<span data-ttu-id="854cd-133">Entity Framework 6 EF プロバイダー以降は、コード ベースの構成を使用して、またはアプリケーションの構成ファイルで登録できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-133">Starting with Entity Framework 6 EF providers can be registered using either code-based configuration or in the application’s config file.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="854cd-134">構成ファイルの登録</span><span class="sxs-lookup"><span data-stu-id="854cd-134">Config file registration</span></span>

<span data-ttu-id="854cd-135">app.config または web.config での EF プロバイダーの登録には、次の形式があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-135">Registration of the EF provider in app.config or web.config has the following format:</span></span>


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

<span data-ttu-id="854cd-136">多くの場合、NuGet から EF プロバイダーがインストールされている場合、NuGet パッケージがこの登録を構成ファイルに自動的に追加します。</span><span class="sxs-lookup"><span data-stu-id="854cd-136">Note that often if the EF provider is installed from NuGet, then the NuGet package will automatically add this registration to the config file.</span></span> <span data-ttu-id="854cd-137">アプリのスタートアップ プロジェクトではないプロジェクトに NuGet パッケージをインストールする場合は、登録をスタートアップ プロジェクトの構成ファイルにコピーする必要があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-137">If you install the NuGet package into a project that is not the startup project for your app, then you may need to copy the registration into the config file for your startup project.</span></span>

<span data-ttu-id="854cd-138">この登録で "invariantName" は、ADO.NET プロバイダーを識別するために使用されるものと同じインバリアント名です。</span><span class="sxs-lookup"><span data-stu-id="854cd-138">The “invariantName” in this registration is the same invariant name used to identify an ADO.NET provider.</span></span> <span data-ttu-id="854cd-139">これは、DbProviderFactories 登録の “invariant” 属性として、および接続文字列の登録の "providerName" 属性として確認できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-139">This can be found as the “invariant” attribute in a DbProviderFactories registration and as the “providerName” attribute in a connection string registration.</span></span> <span data-ttu-id="854cd-140">使用するインバリアント名は、プロバイダーのドキュメントにも含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-140">The invariant name to use should also be included in documentation for the provider.</span></span> <span data-ttu-id="854cd-141">インバリアント名の例として、SQL Server を示す "System.Data.SqlClient" や、SQL Server Compact を示す "System.Data.SqlServerCe.4.0" などがあります。</span><span class="sxs-lookup"><span data-stu-id="854cd-141">Examples of invariant names are “System.Data.SqlClient” for SQL Server and “System.Data.SqlServerCe.4.0” for SQL Server Compact.</span></span>

<span data-ttu-id="854cd-142">この登録の "type" は、"System.Data.Entity.Core.Common.DbProviderServices" から取得されるプロバイダーの種類のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="854cd-142">The “type” in this registration is the assembly-qualified name of the provider type that derives from “System.Data.Entity.Core.Common.DbProviderServices”.</span></span> <span data-ttu-id="854cd-143">たとえば、SQL Compact に使用する文字列は、“System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact” です。</span><span class="sxs-lookup"><span data-stu-id="854cd-143">For example, the string to use for SQL Compact is “System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact”.</span></span> <span data-ttu-id="854cd-144">ここで使用する型は、プロバイダーのドキュメントに含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-144">The type to use here should be included in documentation for the provider.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="854cd-145">コード ベースの登録</span><span class="sxs-lookup"><span data-stu-id="854cd-145">Code-based registration</span></span>

<span data-ttu-id="854cd-146">Entity Framework 6 以降では、EF のアプリケーション全体の構成をコードで指定できます。</span><span class="sxs-lookup"><span data-stu-id="854cd-146">Starting with Entity Framework 6 application-wide configuration for EF can be specified in code.</span></span> <span data-ttu-id="854cd-147">詳細については、「_[Entity Framework - コード ベースの構成](https://msdn.microsoft.com/data/jj680699)_」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="854cd-147">For full details see _[Entity Framework Code-Based Configuration](https://msdn.microsoft.com/data/jj680699)_.</span></span> <span data-ttu-id="854cd-148">コード ベースの構成を使用して、EF プロバイダーを登録する通常の方法では、System.Data.Entity.DbConfiguration から派生する新しいクラスを作成し、DbContext クラスと同じアセンブリ内にそれを配置します。</span><span class="sxs-lookup"><span data-stu-id="854cd-148">The normal way to register an EF provider using code-based configuration is to create a new class that derives from System.Data.Entity.DbConfiguration and place it in the same assembly as your DbContext class.</span></span> <span data-ttu-id="854cd-149">その後で、DbConfiguration クラスが、そのコンストラクターでプロバイダーを登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-149">Your DbConfiguration class should then register the provider in its constructor.</span></span> <span data-ttu-id="854cd-150">たとえば、SQL Compact プロバイダーを登録するための DbConfiguration クラスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="854cd-150">For example, to register the SQL Compact provider the DbConfiguration class looks like this:</span></span>

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

<span data-ttu-id="854cd-151">このコードでは、"SqlCeProviderServices.ProviderInvariantName" は、SQL Server Compact プロバイダーのインバリアント名文字列 ("System.Data.SqlServerCe.4.0") の簡易版であり、SqlCeProviderServices.Instance は、SQL Compact EF プロバイダーのシングルトン インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="854cd-151">In this code “SqlCeProviderServices.ProviderInvariantName” is a convenience for the SQL Server Compact provider invariant name string (“System.Data.SqlServerCe.4.0”) and SqlCeProviderServices.Instance returns the singleton instance of the SQL Compact EF provider.</span></span>

## <a name="what-if-the-provider-i-need-isnt-available"></a><span data-ttu-id="854cd-152">必要なプロバイダーが使用できない場合</span><span class="sxs-lookup"><span data-stu-id="854cd-152">What if the provider I need isn’t available?</span></span>

<span data-ttu-id="854cd-153">以前のバージョンの EF でプロバイダーを使用できる場合、プロバイダーの所有者に連絡して、EF6 のバージョンを作成するように依頼してください。</span><span class="sxs-lookup"><span data-stu-id="854cd-153">If the provider is available for previous versions of EF, then we encourage you to contact the owner of the provider and ask them to create an EF6 version.</span></span> <span data-ttu-id="854cd-154">[EF6 のプロバイダー モデルに関するドキュメント](xref:ef6/fundamentals/providers/provider-model)の参照を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="854cd-154">You should include a reference to the [documentation for the EF6 provider model](xref:ef6/fundamentals/providers/provider-model).</span></span>

## <a name="can-i-write-a-provider-myself"></a><span data-ttu-id="854cd-155">自分でプロバイダーを作成することはできますか</span><span class="sxs-lookup"><span data-stu-id="854cd-155">Can I write a provider myself?</span></span>

<span data-ttu-id="854cd-156">EF のプロバイダーを自分で作成することは可能ですが、簡単なこととは考えないでください。</span><span class="sxs-lookup"><span data-stu-id="854cd-156">It is certainly possible to create an EF provider yourself although it should not be considered a trivial undertaking.</span></span> <span data-ttu-id="854cd-157">前述の EF6 のプロバイダー モデルに関するリンクは、出発点として適しています。</span><span class="sxs-lookup"><span data-stu-id="854cd-157">The the link above about the EF6 provider model is a good place to start.</span></span> <span data-ttu-id="854cd-158">[EF のオープン ソース コードベース](https://github.com/aspnet/EntityFramework6)に含まれている SQL Server および SQL CE プロバイダーのコードを開始点または参照として使用すると役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="854cd-158">You may also find it useful to use the code for the SQL Server and SQL CE provider included in the [EF open source codebase](https://github.com/aspnet/EntityFramework6) as a starting point or for reference.</span></span>

<span data-ttu-id="854cd-159">EF6 以降、EF プロバイダーと基になる ADO.NET プロバイダーとの結びつきが緩やかになっていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="854cd-159">Note that starting with EF6 the EF provider is less tightly coupled to the underlying ADO.NET provider.</span></span> <span data-ttu-id="854cd-160">これにより、ADO.NET クラスの書き込みまたはラップを行わずに、EF プロバイダーを簡単に記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="854cd-160">This makes it easier to write an EF provider without needing to write or wrap the ADO.NET classes.</span></span>
