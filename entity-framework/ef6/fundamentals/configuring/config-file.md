---
title: 構成ファイルの設定-EF6
description: Entity Framework 6 の構成ファイル設定
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: fc991810e93840c27e6631dfb5bc1796c1328d37
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063323"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="d06f4-103">構成ファイルの設定</span><span class="sxs-lookup"><span data-stu-id="d06f4-103">Configuration File Settings</span></span>
<span data-ttu-id="d06f4-104">Entity Framework では、構成ファイルからいくつかの設定を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="d06f4-105">一般的な EF では、"構成よりも規約" の原則に従います。この投稿で説明されているすべての設定は既定の動作であるため、既定で要件を満たさなくなった場合にのみ設定を変更することを考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="d06f4-106">Code-Based 代替手段</span><span class="sxs-lookup"><span data-stu-id="d06f4-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="d06f4-107">これらの設定はすべて、コードを使用して適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="d06f4-108">EF6 以降では、コードから構成を適用するための一元的な方法を提供する [コードベースの構成](xref:ef6/fundamentals/configuring/code-based)が導入されました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="d06f4-109">EF6 より前でも、構成はコードから適用できますが、さまざまな Api を使用してさまざまな領域を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="d06f4-110">[構成ファイル] オプションを使用すると、コードを更新しなくても、配置時にこれらの設定を簡単に変更できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="d06f4-111">Entity Framework 構成セクション</span><span class="sxs-lookup"><span data-stu-id="d06f4-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="d06f4-112">EF 4.1 以降では、構成ファイルの **appSettings** セクションを使用して、コンテキストのデータベース初期化子を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="d06f4-113">EF 4.3 では、新しい設定を処理するためのカスタム **Entityframework** セクションが導入されました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="d06f4-114">Entity Framework では、古い形式を使用して設定されたデータベース初期化子を引き続き認識しますが、可能な限り新しい形式に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d06f4-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="d06f4-115">Entityframework の NuGet パッケージをインストールすると、プロジェクトの構成ファイルに **entityframework** セクションが自動的に追加されました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

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

## <a name="connection-strings"></a><span data-ttu-id="d06f4-116">接続文字列</span><span class="sxs-lookup"><span data-stu-id="d06f4-116">Connection Strings</span></span>  

<span data-ttu-id="d06f4-117">[このページ](xref:ef6/fundamentals/configuring/connection-strings) では、構成ファイル内の接続文字列など、使用するデータベースを Entity Framework によって決定する方法の詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="d06f4-118">接続文字列は標準の **connectionStrings** 要素に含まれ、 **entityframework** セクションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d06f4-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="d06f4-119">Code First ベースのモデルでは、通常の ADO.NET 接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="d06f4-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="d06f4-121">EF デザイナーベースのモデルでは、特殊な EF 接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="d06f4-122">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-122">For example:</span></span>  

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

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="d06f4-123">Code-Based 構成の種類 (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="d06f4-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="d06f4-124">EF6 以降では、アプリケーションの [コードベースの構成](xref:ef6/fundamentals/configuring/code-based) に使用する EF の dbconfiguration を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="d06f4-125">ほとんどの場合、EF によって自動的に DbConfiguration が検出されるため、この設定を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d06f4-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="d06f4-126">構成ファイルで DbConfiguration を指定する必要がある場合の詳細については、「[コードベースの構成](xref:ef6/fundamentals/configuring/code-based)」の「 **Dbconfiguration の移動**」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d06f4-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="d06f4-127">DbConfiguration 型を設定するには、 **codeConfigurationType** 要素にアセンブリ修飾型名を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="d06f4-128">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="d06f4-129">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="d06f4-130">EF データベースプロバイダー (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="d06f4-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="d06f4-131">EF6 より前は、データベースプロバイダーの Entity Framework 固有の部分をコア ADO.NET プロバイダーの一部として含める必要がありました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="d06f4-132">EF6 以降では、EF 固有の部分が管理され、個別に登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="d06f4-133">通常、プロバイダーを自分で登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d06f4-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="d06f4-134">これは通常、プロバイダーをインストールするときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="d06f4-135">プロバイダーは、 **Entityframework**セクションの**providers**子セクションに**プロバイダー**要素を含めることによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="d06f4-136">プロバイダーエントリには、次の2つの必須の属性があります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="d06f4-137">**示す invariantname** は、この EF プロバイダーが対象とするコア ADO.NET プロバイダーを識別します</span><span class="sxs-lookup"><span data-stu-id="d06f4-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="d06f4-138">**type** は、EF プロバイダーの実装のアセンブリ修飾型名です。</span><span class="sxs-lookup"><span data-stu-id="d06f4-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="d06f4-139">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="d06f4-140">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="d06f4-141">例として、Entity Framework をインストールするときに既定の SQL Server プロバイダーを登録するために作成されたエントリを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="d06f4-142">インターセプター (EF 6.1 以降)</span><span class="sxs-lookup"><span data-stu-id="d06f4-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="d06f4-143">EF 6.1 以降では、構成ファイルにインターセプターを登録できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="d06f4-144">インターセプターを使用すると、EF がデータベースクエリの実行や接続の開始などの特定の操作を実行するときに、追加のロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="d06f4-145">インターセプターは、 **Entityframework**セクションの**インターセプター子セクションの下に\*\*\*\*インターセプター**要素を含めることによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="d06f4-146">たとえば、次の構成では、すべてのデータベース操作をコンソールに記録する組み込みの **Databaselogger** インターセプターが登録されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="d06f4-147">ファイルへのデータベース操作のログ記録 (EF 6.1 以降)</span><span class="sxs-lookup"><span data-stu-id="d06f4-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="d06f4-148">構成ファイルを使用したインターセプターの登録は、問題のデバッグを支援するために、既存のアプリケーションにログ記録を追加する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="d06f4-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="d06f4-149">**Databaselogger** では、ファイル名をコンストラクターパラメーターとして指定することによって、ファイルへのログ記録をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d06f4-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="d06f4-150">既定では、これにより、アプリが開始されるたびにログファイルが新しいファイルで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="d06f4-151">既に存在する場合は、ログファイルに追加するには、次のようにを使用します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-151">To instead append to the log file if it already exists use something like:</span></span>  

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

<span data-ttu-id="d06f4-152">**Databaselogger**およびインターセプターの登録の詳細については、ブログ投稿「 [EF 6.1: 再コンパイルせずにログ記録を有効](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)にする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d06f4-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="d06f4-153">既定の接続ファクトリの Code First</span><span class="sxs-lookup"><span data-stu-id="d06f4-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="d06f4-154">[構成] セクションでは、コンテキストに使用するデータベースを検索するために使用 Code First 既定の接続ファクトリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="d06f4-155">既定の接続ファクトリは、コンテキストの構成ファイルに接続文字列が追加されていない場合にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="d06f4-156">EF NuGet パッケージをインストールすると、インストールされているものに応じて、SQL Express または LocalDB を指す既定の接続ファクトリが登録されました。</span><span class="sxs-lookup"><span data-stu-id="d06f4-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="d06f4-157">接続ファクトリを設定するには、 **Defaultconnectionfactory** 要素にアセンブリ修飾型名を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="d06f4-158">アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="d06f4-159">必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="d06f4-160">独自の既定の接続ファクトリを設定する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="d06f4-161">上の例では、カスタムファクトリにパラメーターなしのコンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="d06f4-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="d06f4-162">必要に応じて、 **parameters** 要素を使用してコンストラクターのパラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="d06f4-163">たとえば、Entity Framework に含まれる SqlCeConnectionFactory では、プロバイダーの不変名をコンストラクターに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="d06f4-164">プロバイダー不変名は、使用する SQL Compact のバージョンを識別します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="d06f4-165">次の構成では、コンテキストによって既定で SQL Compact バージョン4.0 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="d06f4-166">既定の接続ファクトリを設定していない場合、Code First はを指す SqlConnectionFactory を使用し `.\SQLEXPRESS` ます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="d06f4-167">SqlConnectionFactory には、接続文字列の一部をオーバーライドできるコンストラクターも用意されています。</span><span class="sxs-lookup"><span data-stu-id="d06f4-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="d06f4-168">以外の SQL Server インスタンスを使用する場合は `.\SQLEXPRESS` 、このコンストラクターを使用してサーバーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="d06f4-169">次の構成では、明示的な接続文字列が設定されていないコンテキストに対して Code First が **Mydatabaseserver** を使用するようになります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="d06f4-170">既定では、コンストラクターの引数が文字列型であると想定されています。</span><span class="sxs-lookup"><span data-stu-id="d06f4-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="d06f4-171">Type 属性を使用してこれを変更できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="d06f4-172">データベース初期化子</span><span class="sxs-lookup"><span data-stu-id="d06f4-172">Database Initializers</span></span>  

<span data-ttu-id="d06f4-173">データベース初期化子は、コンテキスト単位で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="d06f4-174">これらは、 **コンテキスト** 要素を使用して構成ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="d06f4-175">この要素は、アセンブリ修飾名を使用して、構成されているコンテキストを識別します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="d06f4-176">既定では、Code First のコンテキストは、CreateDatabaseIfNotExists 初期化子を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="d06f4-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="d06f4-177">データベースの初期化を無効にするために使用できる、**コンテキスト**要素に**disabledatabaseinitialization**属性があります。</span><span class="sxs-lookup"><span data-stu-id="d06f4-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="d06f4-178">たとえば、次の構成では、Blog のデータベース初期化が無効になっています。 MyAssembly.dll で定義されているブログのコンテキストコンテキストです。</span><span class="sxs-lookup"><span data-stu-id="d06f4-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="d06f4-179">**Databaseinitializer**要素を使用して、カスタム初期化子を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="d06f4-180">コンストラクターのパラメーターは、既定の接続ファクトリと同じ構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

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

<span data-ttu-id="d06f4-181">Entity Framework に含まれている汎用データベース初期化子のいずれかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="d06f4-182">**Type**属性は、ジェネリック型の .NET Framework 形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="d06f4-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="d06f4-183">たとえば、Code First Migrations を使用している場合は、初期化子を使用してデータベースを自動的に移行するように構成でき `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` ます。</span><span class="sxs-lookup"><span data-stu-id="d06f4-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
