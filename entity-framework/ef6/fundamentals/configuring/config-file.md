---
title: 構成ファイルの設定 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: faba4e406b9f26f5bed6149f75c59da362d84692
ms.sourcegitcommit: 15022dd06d919c29b1189c82611ea32f9fdc6617
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47415784"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="ab294-102">構成ファイルの設定</span><span class="sxs-lookup"><span data-stu-id="ab294-102">Configuration File Settings</span></span>
<span data-ttu-id="ab294-103">Entity Framework は、さまざまな設定を構成ファイルから指定できます。</span><span class="sxs-lookup"><span data-stu-id="ab294-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="ab294-104">一般に EF に依存して '設定より規約' 原則: この記事で説明されているすべての設定を既定の動作、のみ、既定値が不要になった、要件を満たすときに、設定を変更する方法について心配する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab294-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="ab294-105">コード ベースの待機</span><span class="sxs-lookup"><span data-stu-id="ab294-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="ab294-106">これらすべての設定も適用できますコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="ab294-107">以降では、EF6 を導入しました[コード ベースの構成](code-based.md)、コードからの構成の適用の主要な方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="ab294-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="ab294-108">EF6 より前は、構成をコードから適用できますが、さまざまな Api を使用して、さまざまな領域を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab294-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="ab294-109">構成ファイルのオプションは、これらの設定をコードを更新することがなく展開時に簡単に変更できます。</span><span class="sxs-lookup"><span data-stu-id="ab294-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="ab294-110">Entity Framework の構成セクション</span><span class="sxs-lookup"><span data-stu-id="ab294-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="ab294-111">データベースの初期化子を使用してコンテキストが設定でした EF4.1 で始まる、 **appSettings**構成ファイルのセクション。</span><span class="sxs-lookup"><span data-stu-id="ab294-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="ab294-112">カスタムに導入されています EF 4.3 **entityFramework**新しい設定を処理するセクション。</span><span class="sxs-lookup"><span data-stu-id="ab294-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="ab294-113">Entity Framework は、古い形式を使用して設定データベース初期化子を認識が可能であれば、新しい形式への移行をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ab294-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="ab294-114">**EntityFramework** EntityFramework NuGet パッケージをインストールしたときに、セクションは、プロジェクトの構成ファイルに追加自動的にされました。</span><span class="sxs-lookup"><span data-stu-id="ab294-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

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

## <a name="connection-strings"></a><span data-ttu-id="ab294-115">接続文字列</span><span class="sxs-lookup"><span data-stu-id="ab294-115">Connection Strings</span></span>  

<span data-ttu-id="ab294-116">[このページ](~/ef6/fundamentals/configuring/connection-strings.md)構成ファイルで接続文字列を含む Entity Framework が使用するデータベースを決定する方法の詳細を提供します。</span><span class="sxs-lookup"><span data-stu-id="ab294-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="ab294-117">接続文字列は、標準の移動**connectionStrings**要素は必要ありません、 **entityFramework**セクション。</span><span class="sxs-lookup"><span data-stu-id="ab294-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="ab294-118">最初に基づくコード モデルでは、通常の ADO.NET 接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="ab294-119">例えば:</span><span class="sxs-lookup"><span data-stu-id="ab294-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="ab294-120">EF デザイナー ベースのモデル使用特殊な EF 接続文字列。</span><span class="sxs-lookup"><span data-stu-id="ab294-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="ab294-121">例えば:</span><span class="sxs-lookup"><span data-stu-id="ab294-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="ab294-122">コード ベースの構成の種類 (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="ab294-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="ab294-123">EF6 から始めて、ef を使用する DbConfiguration を指定できます[コード ベースの構成](code-based.md)アプリケーションでします。</span><span class="sxs-lookup"><span data-stu-id="ab294-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="ab294-124">ほとんどの場合は、EF は、DbConfiguration を自動的に検出すると、この設定を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ab294-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="ab294-125">構成ファイルで DbConfiguration を指定する必要がある場合の詳細を参照してください、**移動 DbConfiguration**の[コード ベースの構成](code-based.md)します。</span><span class="sxs-lookup"><span data-stu-id="ab294-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="ab294-126">内のアセンブリ修飾型名を指定する DbConfiguration 型を設定する、 **codeConfigurationType**要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="ab294-127">アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="ab294-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="ab294-128">必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。</span><span class="sxs-lookup"><span data-stu-id="ab294-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="ab294-129">EF データベース プロバイダー (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="ab294-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="ab294-130">EF6 では、前にデータベース プロバイダーの Entity Framework に固有の部分は、コアの ADO.NET プロバイダーの一部として含まれる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab294-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="ab294-131">EF6 から始めて、EF の特定の部分ようになりました管理され、個別に登録します。</span><span class="sxs-lookup"><span data-stu-id="ab294-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="ab294-132">通常は、自分でプロバイダーを登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ab294-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="ab294-133">これは通常実行されます、プロバイダーをインストールするときにします。</span><span class="sxs-lookup"><span data-stu-id="ab294-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="ab294-134">含めることでプロバイダーが登録されている、**プロバイダー**の下の要素、**プロバイダー**子セクションの**entityFramework**セクション。</span><span class="sxs-lookup"><span data-stu-id="ab294-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="ab294-135">プロバイダーのエントリに必要な 2 つの属性があります。</span><span class="sxs-lookup"><span data-stu-id="ab294-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="ab294-136">**invariantName**コア ADO.NET プロバイダーを識別しますこのプロバイダーが対象と EF。</span><span class="sxs-lookup"><span data-stu-id="ab294-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="ab294-137">**型**EF プロバイダーの実装のアセンブリ修飾型名には</span><span class="sxs-lookup"><span data-stu-id="ab294-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="ab294-138">アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="ab294-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="ab294-139">必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。</span><span class="sxs-lookup"><span data-stu-id="ab294-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="ab294-140">例としては、次に Entity Framework をインストールするときに、既定の SQL Server プロバイダーを登録するために作成するエントリを示します。</span><span class="sxs-lookup"><span data-stu-id="ab294-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="ab294-141">インターセプター (EF6.1 以降)</span><span class="sxs-lookup"><span data-stu-id="ab294-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="ab294-142">EF6.1 で始まると、インターセプターが構成ファイルに登録できます。</span><span class="sxs-lookup"><span data-stu-id="ab294-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="ab294-143">インターセプターを使用すると、EF は接続などを開く、データベース クエリの実行など、特定の操作を実行するときに、追加のロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ab294-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="ab294-144">インターセプターが含めることによって登録されている、**インターセプター**の下の要素、**インターセプター**子セクションの**entityFramework**セクション。</span><span class="sxs-lookup"><span data-stu-id="ab294-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="ab294-145">たとえば、次の構成に登録組み込み**DatabaseLogger**インターセプターをコンソールにすべてのデータベース操作が記録されます。</span><span class="sxs-lookup"><span data-stu-id="ab294-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="ab294-146">ファイル (EF6.1 以降) にデータベース操作のログ記録</span><span class="sxs-lookup"><span data-stu-id="ab294-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="ab294-147">インターセプター構成ファイルを使用して登録すると、問題をデバッグする際に既存のアプリケーションにログ記録を追加するときに便利です。</span><span class="sxs-lookup"><span data-stu-id="ab294-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="ab294-148">**DatabaseLogger**コンス トラクター パラメーターとしてファイル名を指定することで、ファイルへのログ記録をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ab294-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="ab294-149">既定では、アプリが起動されるたびに新しいファイルで上書きするログ ファイルは、なります。</span><span class="sxs-lookup"><span data-stu-id="ab294-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="ab294-150">代わりに、ログに追加するには、ファイルが既に存在する場合などを使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-150">To instead append to the log file if it already exists use something like:</span></span>  

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

<span data-ttu-id="ab294-151">に関する追加情報について**DatabaseLogger**インターセプターを登録するには、ブログの投稿を参照してくださいと[EF 6.1: 再コンパイルせずにログ記録をオン](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)します。</span><span class="sxs-lookup"><span data-stu-id="ab294-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="ab294-152">最初の既定の接続ファクトリ コード</span><span class="sxs-lookup"><span data-stu-id="ab294-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="ab294-153">構成セクションでは、Code First がコンテキストを使用するデータベースを検索に使用する既定の接続ファクトリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ab294-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="ab294-154">既定の接続ファクトリは、コンテキストの構成ファイルに接続文字列が追加されていない場合にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="ab294-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="ab294-155">EF の NuGet パッケージをインストールしたときに SQL Express または LocalDB は、インストールしたかに応じてのいずれかを示す既定の接続ファクトリが登録されました。</span><span class="sxs-lookup"><span data-stu-id="ab294-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="ab294-156">内のアセンブリ修飾型名を指定する接続ファクトリを設定する、 **defaultConnectionFactory**要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-156">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="ab294-157">アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="ab294-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="ab294-158">必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。</span><span class="sxs-lookup"><span data-stu-id="ab294-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="ab294-159">独自の既定の接続ファクトリを設定する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ab294-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="ab294-160">上記の例では、パラメーターなしのコンス トラクターを持つカスタムのファクトリが必要です。</span><span class="sxs-lookup"><span data-stu-id="ab294-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="ab294-161">使用してコンス トラクターのパラメーターを指定できますが必要な場合、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="ab294-162">たとえば、Entity Framework に含まれていると、SqlCeConnectionFactory コンス トラクターにプロバイダーの不変名を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab294-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="ab294-163">プロバイダーの不変名では、SQL Compact を使用するのバージョンを識別します。</span><span class="sxs-lookup"><span data-stu-id="ab294-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="ab294-164">次の構成は、既定ではバージョン 4.0 SQL Compact を使用するコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="ab294-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="ab294-165">既定の接続ファクトリを設定していない場合は、Code First を使用して指す、SqlConnectionFactory`.\SQLEXPRESS`します。</span><span class="sxs-lookup"><span data-stu-id="ab294-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="ab294-166">SqlConnectionFactory では、接続文字列の各部をオーバーライドできるようにするコンス トラクターもあります。</span><span class="sxs-lookup"><span data-stu-id="ab294-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="ab294-167">以外の SQL Server インスタンスを使用する場合`.\SQLEXPRESS`このコンス トラクターを使用してサーバーを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ab294-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="ab294-168">Code First を使用すると、次の構成、 **MyDatabaseServer**コンテキスト設定明示的な接続文字列がないです。</span><span class="sxs-lookup"><span data-stu-id="ab294-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="ab294-169">既定では、コンス トラクターの引数が文字列型ことが前提です。</span><span class="sxs-lookup"><span data-stu-id="ab294-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="ab294-170">型の属性を使用して、これを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="ab294-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="ab294-171">データベースの初期化子</span><span class="sxs-lookup"><span data-stu-id="ab294-171">Database Initializers</span></span>  

<span data-ttu-id="ab294-172">データベースの初期化子は、コンテキストごとに構成されます。</span><span class="sxs-lookup"><span data-stu-id="ab294-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="ab294-173">構成ファイルを使用して設定できる、**コンテキスト**要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="ab294-174">この要素は、構成されているコンテキストを識別するために、アセンブリ修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="ab294-175">既定では、コンテキストが Code First は CreateDatabaseIfNotExists 初期化子を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="ab294-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="ab294-176">**DisableDatabaseInitialization**属性を**コンテキスト**データベースの初期化を無効にするために使用する要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="ab294-177">たとえば、次の構成では、MyAssembly.dll で定義されている Blogging.BlogContext コンテキストのデータベースの初期化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="ab294-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="ab294-178">使用することができます、 **databaseInitializer**カスタム初期化子を設定する要素。</span><span class="sxs-lookup"><span data-stu-id="ab294-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="ab294-179">コンス トラクターのパラメーターは、既定の接続ファクトリとして同じ構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

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

<span data-ttu-id="ab294-180">Entity Framework に含まれている汎用データベース初期化子のいずれかを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="ab294-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="ab294-181">**型**属性はジェネリック型の .NET Framework 形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab294-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="ab294-182">たとえば、Code First Migrations を使用している場合は、使用して自動的に移行するデータベースを構成できます、`MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`初期化子。</span><span class="sxs-lookup"><span data-stu-id="ab294-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
