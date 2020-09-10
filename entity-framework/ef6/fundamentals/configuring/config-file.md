---
title: 構成ファイルの設定-EF6
description: Entity Framework 6 の構成ファイル設定
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: ac257c0da8da4dff852ec24f7de91c62c68f92d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618484"
---
# <a name="configuration-file-settings"></a>構成ファイルの設定
Entity Framework では、構成ファイルからいくつかの設定を指定できます。 一般的な EF では、"構成よりも規約" の原則に従います。この投稿で説明されているすべての設定は既定の動作であるため、既定で要件を満たさなくなった場合にのみ設定を変更することを考慮する必要があります。  

## <a name="a-code-based-alternative"></a>コードベースの代替手段  

これらの設定はすべて、コードを使用して適用することもできます。 EF6 以降では、コードから構成を適用するための一元的な方法を提供する [コードベースの構成](xref:ef6/fundamentals/configuring/code-based)が導入されました。 EF6 より前でも、構成はコードから適用できますが、さまざまな Api を使用してさまざまな領域を構成する必要があります。 [構成ファイル] オプションを使用すると、コードを更新しなくても、配置時にこれらの設定を簡単に変更できます。

## <a name="the-entity-framework-configuration-section"></a>Entity Framework 構成セクション  

EF 4.1 以降では、構成ファイルの **appSettings** セクションを使用して、コンテキストのデータベース初期化子を設定できます。 EF 4.3 では、新しい設定を処理するためのカスタム **Entityframework** セクションが導入されました。 Entity Framework では、古い形式を使用して設定されたデータベース初期化子を引き続き認識しますが、可能な限り新しい形式に移行することをお勧めします。

Entityframework の NuGet パッケージをインストールすると、プロジェクトの構成ファイルに **entityframework** セクションが自動的に追加されました。  

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

## <a name="connection-strings"></a>接続文字列  

[このページ](xref:ef6/fundamentals/configuring/connection-strings) では、構成ファイル内の接続文字列など、使用するデータベースを Entity Framework によって決定する方法の詳細について説明します。  

接続文字列は標準の **connectionStrings** 要素に含まれ、 **entityframework** セクションは必要ありません。  

Code First ベースのモデルでは、通常の ADO.NET 接続文字列を使用します。 次に例を示します。  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

EF デザイナーベースのモデルでは、特殊な EF 接続文字列を使用します。 次に例を示します。  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>コードベースの構成の種類 (EF6 以降)  

EF6 以降では、アプリケーションの [コードベースの構成](xref:ef6/fundamentals/configuring/code-based) に使用する EF の dbconfiguration を指定できます。 ほとんどの場合、EF によって自動的に DbConfiguration が検出されるため、この設定を指定する必要はありません。 構成ファイルで DbConfiguration を指定する必要がある場合の詳細については、「[コードベースの構成](xref:ef6/fundamentals/configuring/code-based)」の「 **Dbconfiguration の移動**」セクションを参照してください。  

DbConfiguration 型を設定するには、 **codeConfigurationType** 要素にアセンブリ修飾型名を指定します。  

> [!NOTE]
> アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。 必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF データベースプロバイダー (EF6 以降)  

EF6 より前は、データベースプロバイダーの Entity Framework 固有の部分をコア ADO.NET プロバイダーの一部として含める必要がありました。 EF6 以降では、EF 固有の部分が管理され、個別に登録されるようになりました。  

通常、プロバイダーを自分で登録する必要はありません。 これは通常、プロバイダーをインストールするときに実行されます。  

プロバイダーは、 **Entityframework**セクションの**providers**子セクションに**プロバイダー**要素を含めることによって登録されます。 プロバイダーエントリには、次の2つの必須の属性があります。  

- **示す invariantname** は、この EF プロバイダーが対象とするコア ADO.NET プロバイダーを識別します  
- **type** は、EF プロバイダーの実装のアセンブリ修飾型名です。  

> [!NOTE]
> アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。 必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。  

例として、Entity Framework をインストールするときに既定の SQL Server プロバイダーを登録するために作成されたエントリを次に示します。  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>インターセプター (EF 6.1 以降)  

EF 6.1 以降では、構成ファイルにインターセプターを登録できます。 インターセプターを使用すると、EF がデータベースクエリの実行や接続の開始などの特定の操作を実行するときに、追加のロジックを実行できます。  

インターセプターは、 **Entityframework**セクションの**インターセプター子セクションの下に****インターセプター**要素を含めることによって登録されます。 たとえば、次の構成では、すべてのデータベース操作をコンソールに記録する組み込みの **Databaselogger** インターセプターが登録されます。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>ファイルへのデータベース操作のログ記録 (EF 6.1 以降)  

構成ファイルを使用したインターセプターの登録は、問題のデバッグを支援するために、既存のアプリケーションにログ記録を追加する場合に特に便利です。 **Databaselogger** では、ファイル名をコンストラクターパラメーターとして指定することによって、ファイルへのログ記録をサポートしています。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

既定では、これにより、アプリが開始されるたびにログファイルが新しいファイルで上書きされます。 既に存在する場合は、ログファイルに追加するには、次のようにを使用します。  

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

**Databaselogger**およびインターセプターの登録の詳細については、ブログ投稿「 [EF 6.1: 再コンパイルせずにログ記録を有効](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)にする」を参照してください。  

## <a name="code-first-default-connection-factory"></a>既定の接続ファクトリの Code First  

[構成] セクションでは、コンテキストに使用するデータベースを検索するために使用 Code First 既定の接続ファクトリを指定できます。 既定の接続ファクトリは、コンテキストの構成ファイルに接続文字列が追加されていない場合にのみ使用されます。  

EF NuGet パッケージをインストールすると、インストールされているものに応じて、SQL Express または LocalDB を指す既定の接続ファクトリが登録されました。  

接続ファクトリを設定するには、 **Defaultconnectionfactory** 要素にアセンブリ修飾型名を指定します。  

> [!NOTE]
> アセンブリ修飾名は、名前空間の修飾名の後にコンマを付け、その後に型が存在するアセンブリを指定します。 必要に応じて、アセンブリのバージョン、カルチャ、および公開キートークンを指定することもできます。  

独自の既定の接続ファクトリを設定する例を次に示します。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

上の例では、カスタムファクトリにパラメーターなしのコンストラクターが必要です。 必要に応じて、 **parameters** 要素を使用してコンストラクターのパラメーターを指定できます。  

たとえば、Entity Framework に含まれる SqlCeConnectionFactory では、プロバイダーの不変名をコンストラクターに指定する必要があります。 プロバイダー不変名は、使用する SQL Compact のバージョンを識別します。 次の構成では、コンテキストによって既定で SQL Compact バージョン4.0 が使用されます。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

既定の接続ファクトリを設定していない場合、Code First はを指す SqlConnectionFactory を使用し `.\SQLEXPRESS` ます。 SqlConnectionFactory には、接続文字列の一部をオーバーライドできるコンストラクターも用意されています。 以外の SQL Server インスタンスを使用する場合は `.\SQLEXPRESS` 、このコンストラクターを使用してサーバーを設定できます。  

次の構成では、明示的な接続文字列が設定されていないコンテキストに対して Code First が **Mydatabaseserver** を使用するようになります。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

既定では、コンストラクターの引数が文字列型であると想定されています。 Type 属性を使用してこれを変更できます。  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>データベース初期化子  

データベース初期化子は、コンテキスト単位で構成されます。 これらは、 **コンテキスト** 要素を使用して構成ファイルで設定できます。 この要素は、アセンブリ修飾名を使用して、構成されているコンテキストを識別します。  

既定では、Code First のコンテキストは、CreateDatabaseIfNotExists 初期化子を使用するように構成されています。 データベースの初期化を無効にするために使用できる、**コンテキスト**要素に**disabledatabaseinitialization**属性があります。  

たとえば、次の構成では、Blog のデータベース初期化が無効になっています。 MyAssembly.dll で定義されているブログのコンテキストコンテキストです。  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

**Databaseinitializer**要素を使用して、カスタム初期化子を設定できます。  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

コンストラクターのパラメーターは、既定の接続ファクトリと同じ構文を使用します。  

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

Entity Framework に含まれている汎用データベース初期化子のいずれかを構成できます。 **Type**属性は、ジェネリック型の .NET Framework 形式を使用します。  

たとえば、Code First Migrations を使用している場合は、初期化子を使用してデータベースを自動的に移行するように構成でき `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` ます。  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
