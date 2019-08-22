---
title: 構成ファイルの設定-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 86389e4a3a3bac46e2a4cf2da648a4b19e29f3c3
ms.sourcegitcommit: 299011fc4bd576eed58a4274f967639fa13fec53
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886553"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="16383-102">構成ファイルの設定</span><span class="sxs-lookup"><span data-stu-id="16383-102">Configuration File Settings</span></span>
<span data-ttu-id="16383-103">Entity Framework では、構成ファイルからいくつかの設定を指定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="16383-104">一般的な EF では、"構成よりも規約" の原則に従います。この投稿で説明されているすべての設定は既定の動作であるため、既定で要件を満たさなくなった場合にのみ設定を変更することを考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16383-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="16383-105">コードベースの代替手段</span><span class="sxs-lookup"><span data-stu-id="16383-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="16383-106">これらの設定はすべて、コードを使用して適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="16383-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="16383-107">EF6 以降では、コードから構成を適用するための一元的な方法を提供する[コードベースの構成](code-based.md)が導入されました。</span><span class="sxs-lookup"><span data-stu-id="16383-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="16383-108">EF6 より前でも、構成はコードから適用できますが、さまざまな Api を使用してさまざまな領域を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16383-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="16383-109">[構成ファイル] オプションを使用すると、コードを更新しなくても、配置時にこれらの設定を簡単に変更できます。</span><span class="sxs-lookup"><span data-stu-id="16383-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="16383-110">Entity Framework 構成セクション</span><span class="sxs-lookup"><span data-stu-id="16383-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="16383-111">EF 4.1 以降では、構成ファイルの**appSettings**セクションを使用して、コンテキストのデータベース初期化子を設定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="16383-112">EF 4.3 では、新しい設定を処理するためのカスタム**Entityframework**セクションが導入されました。</span><span class="sxs-lookup"><span data-stu-id="16383-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="16383-113">Entity Framework では、古い形式を使用して設定されたデータベース初期化子を引き続き認識しますが、可能な限り新しい形式に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="16383-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="16383-114">Entityframework の NuGet パッケージをインストールすると、プロジェクトの構成ファイルに**entityframework**セクションが自動的に追加されました。</span><span class="sxs-lookup"><span data-stu-id="16383-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="16383-115">接続文字列</span><span class="sxs-lookup"><span data-stu-id="16383-115">Connection Strings</span></span>  

<span data-ttu-id="16383-116">[このページ](~/ef6/fundamentals/configuring/connection-strings.md)では、構成ファイル内の接続文字列など、使用するデータベースを Entity Framework によって決定する方法の詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="16383-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="16383-117">接続文字列は標準の**connectionStrings**要素に含まれ、 **entityframework**セクションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="16383-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="16383-118">Code First ベースのモデルでは、通常の ADO.NET 接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="16383-119">例えば:</span><span class="sxs-lookup"><span data-stu-id="16383-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="16383-120">EF デザイナーベースのモデルでは、特殊な EF 接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="16383-121">例えば:</span><span class="sxs-lookup"><span data-stu-id="16383-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="16383-122">コードベースの構成の種類 (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="16383-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="16383-123">EF6 以降では、アプリケーションの[コードベースの構成](code-based.md)に使用する EF の dbconfiguration を指定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="16383-124">ほとんどの場合、EF によって自動的に DbConfiguration が検出されるため、この設定を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="16383-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="16383-125">構成ファイルで DbConfiguration を指定する必要がある場合の詳細については、「[コードベースの構成](code-based.md)」の「 **Dbconfiguration の移動**」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16383-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="16383-126">DbConfiguration 型を設定するには、 **codeConfigurationType**要素にアセンブリ修飾型名を指定します。</span><span class="sxs-lookup"><span data-stu-id="16383-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="16383-127">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="16383-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="16383-128">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16383-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="16383-129">EF データベースプロバイダー (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="16383-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="16383-130">EF6 より前は、データベースプロバイダーの Entity Framework 固有の部分をコア ADO.NET プロバイダーの一部として含める必要がありました。</span><span class="sxs-lookup"><span data-stu-id="16383-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="16383-131">EF6 以降では、EF 固有の部分が管理され、個別に登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="16383-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="16383-132">通常、プロバイダーを自分で登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="16383-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="16383-133">これは通常、プロバイダーをインストールするときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="16383-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="16383-134">プロバイダーは、 **Entityframework**セクションの**providers**子セクションに**プロバイダー**要素を含めることによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="16383-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="16383-135">プロバイダーエントリには、次の2つの必須の属性があります。</span><span class="sxs-lookup"><span data-stu-id="16383-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="16383-136">**示す invariantname**は、この EF プロバイダーが対象とするコア ADO.NET プロバイダーを識別します</span><span class="sxs-lookup"><span data-stu-id="16383-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="16383-137">**type**は、EF プロバイダーの実装のアセンブリ修飾型名です。</span><span class="sxs-lookup"><span data-stu-id="16383-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="16383-138">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="16383-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="16383-139">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16383-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="16383-140">例として、Entity Framework をインストールするときに既定の SQL Server プロバイダーを登録するために作成されたエントリを次に示します。</span><span class="sxs-lookup"><span data-stu-id="16383-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="16383-141">インターセプター (EF 6.1 以降)</span><span class="sxs-lookup"><span data-stu-id="16383-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="16383-142">EF 6.1 以降では、構成ファイルにインターセプターを登録できます。</span><span class="sxs-lookup"><span data-stu-id="16383-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="16383-143">インターセプターを使用すると、EF がデータベースクエリの実行や接続の開始などの特定の操作を実行するときに、追加のロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="16383-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="16383-144">インターセプターは、 **Entityframework**セクションのインターセプター子セクションの下に**インターセプター**要素を含めることによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="16383-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="16383-145">たとえば、次の構成では、すべてのデータベース操作をコンソールに記録する組み込みの**Databaselogger**インターセプターが登録されます。</span><span class="sxs-lookup"><span data-stu-id="16383-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="16383-146">ファイルへのデータベース操作のログ記録 (EF 6.1 以降)</span><span class="sxs-lookup"><span data-stu-id="16383-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="16383-147">構成ファイルを使用したインターセプターの登録は、問題のデバッグを支援するために、既存のアプリケーションにログ記録を追加する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="16383-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="16383-148">**Databaselogger**では、ファイル名をコンストラクターパラメーターとして指定することによって、ファイルへのログ記録をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16383-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="16383-149">既定では、これにより、アプリが開始されるたびにログファイルが新しいファイルで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="16383-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="16383-150">既に存在する場合は、ログファイルに追加するには、次のようにを使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-150">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="16383-151">**Databaselogger**およびインターセプターの登録の詳細については、ブログ[投稿「EF 6.1:再コンパイル](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)せずにログ記録をオンにします。</span><span class="sxs-lookup"><span data-stu-id="16383-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="16383-152">既定の接続ファクトリの Code First</span><span class="sxs-lookup"><span data-stu-id="16383-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="16383-153">[構成] セクションでは、コンテキストに使用するデータベースを検索するために使用 Code First 既定の接続ファクトリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="16383-154">既定の接続ファクトリは、コンテキストの構成ファイルに接続文字列が追加されていない場合にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="16383-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="16383-155">EF NuGet パッケージをインストールすると、インストールされているものに応じて、SQL Express または LocalDB を指す既定の接続ファクトリが登録されました。</span><span class="sxs-lookup"><span data-stu-id="16383-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="16383-156">接続ファクトリを設定するには、 **Defaultconnectionfactory**要素にアセンブリ修飾型名を指定します。</span><span class="sxs-lookup"><span data-stu-id="16383-156">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="16383-157">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="16383-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="16383-158">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16383-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="16383-159">独自の既定の接続ファクトリを設定する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="16383-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="16383-160">上の例では、カスタムファクトリにパラメーターなしのコンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="16383-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="16383-161">必要に応じて、 **parameters**要素を使用してコンストラクターのパラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="16383-162">たとえば、Entity Framework に含まれる SqlCeConnectionFactory では、プロバイダーの不変名をコンストラクターに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16383-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="16383-163">プロバイダー不変名は、使用する SQL Compact のバージョンを識別します。</span><span class="sxs-lookup"><span data-stu-id="16383-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="16383-164">次の構成では、コンテキストによって既定で SQL Compact バージョン4.0 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="16383-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="16383-165">既定の接続ファクトリを設定していない場合、Code First はを`.\SQLEXPRESS`指す sqlconnectionfactory を使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="16383-166">SqlConnectionFactory には、接続文字列の一部をオーバーライドできるコンストラクターも用意されています。</span><span class="sxs-lookup"><span data-stu-id="16383-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="16383-167">以外`.\SQLEXPRESS`の SQL Server インスタンスを使用する場合は、このコンストラクターを使用してサーバーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="16383-168">次の構成では、明示的な接続文字列が設定されていないコンテキストに対して Code First が**Mydatabaseserver**を使用するようになります。</span><span class="sxs-lookup"><span data-stu-id="16383-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="16383-169">既定では、コンストラクターの引数が文字列型であると想定されています。</span><span class="sxs-lookup"><span data-stu-id="16383-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="16383-170">Type 属性を使用してこれを変更できます。</span><span class="sxs-lookup"><span data-stu-id="16383-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="16383-171">データベース初期化子</span><span class="sxs-lookup"><span data-stu-id="16383-171">Database Initializers</span></span>  

<span data-ttu-id="16383-172">データベース初期化子は、コンテキスト単位で構成されます。</span><span class="sxs-lookup"><span data-stu-id="16383-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="16383-173">これらは、**コンテキスト**要素を使用して構成ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="16383-174">この要素は、アセンブリ修飾名を使用して、構成されているコンテキストを識別します。</span><span class="sxs-lookup"><span data-stu-id="16383-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="16383-175">既定では、Code First のコンテキストは、CreateDatabaseIfNotExists 初期化子を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="16383-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="16383-176">データベースの初期化を無効にするために使用できる、**コンテキスト**要素に**disabledatabaseinitialization**属性があります。</span><span class="sxs-lookup"><span data-stu-id="16383-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="16383-177">たとえば、次の構成では、MyAssembly で定義されているブログのコンテキストコンテキストのデータベース初期化が無効になっています。</span><span class="sxs-lookup"><span data-stu-id="16383-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="16383-178">**Databaseinitializer**要素を使用して、カスタム初期化子を設定できます。</span><span class="sxs-lookup"><span data-stu-id="16383-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="16383-179">コンストラクターのパラメーターは、既定の接続ファクトリと同じ構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="16383-180">Entity Framework に含まれている汎用データベース初期化子のいずれかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16383-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="16383-181">**Type**属性は、ジェネリック型の .NET Framework 形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="16383-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="16383-182">たとえば、Code First Migrations を使用している場合は、 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`初期化子を使用してデータベースを自動的に移行するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="16383-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
