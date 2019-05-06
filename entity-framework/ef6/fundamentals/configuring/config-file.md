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
# <a name="configuration-file-settings"></a>構成ファイルの設定
Entity Framework は、さまざまな設定を構成ファイルから指定できます。 一般に EF に依存して '設定より規約' 原則: この記事で説明されているすべての設定を既定の動作、のみ、既定値が不要になった、要件を満たすときに、設定を変更する方法について心配する必要があります。  

## <a name="a-code-based-alternative"></a>コード ベースの待機  

これらすべての設定も適用できますコードを使用します。 以降では、EF6 を導入しました[コード ベースの構成](code-based.md)、コードからの構成の適用の主要な方法を提供します。 EF6 より前は、構成をコードから適用できますが、さまざまな Api を使用して、さまざまな領域を構成する必要があります。 構成ファイルのオプションは、これらの設定をコードを更新することがなく展開時に簡単に変更できます。

## <a name="the-entity-framework-configuration-section"></a>Entity Framework の構成セクション  

データベースの初期化子を使用してコンテキストが設定でした EF4.1 で始まる、 **appSettings**構成ファイルのセクション。 カスタムに導入されています EF 4.3 **entityFramework**新しい設定を処理するセクション。 Entity Framework は、古い形式を使用して設定データベース初期化子を認識が可能であれば、新しい形式への移行をお勧めします。

**EntityFramework** EntityFramework NuGet パッケージをインストールしたときに、セクションは、プロジェクトの構成ファイルに追加自動的にされました。  

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

[このページ](~/ef6/fundamentals/configuring/connection-strings.md)構成ファイルで接続文字列を含む Entity Framework が使用するデータベースを決定する方法の詳細を提供します。  

接続文字列は、標準の移動**connectionStrings**要素は必要ありません、 **entityFramework**セクション。  

最初に基づくコード モデルでは、通常の ADO.NET 接続文字列を使用します。 例えば:  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

EF デザイナー ベースのモデル使用特殊な EF 接続文字列。 例えば:  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>コード ベースの構成の種類 (EF6 以降)  

EF6 から始めて、ef を使用する DbConfiguration を指定できます[コード ベースの構成](code-based.md)アプリケーションでします。 ほとんどの場合は、EF は、DbConfiguration を自動的に検出すると、この設定を指定する必要はありません。 構成ファイルで DbConfiguration を指定する必要がある場合の詳細を参照してください、**移動 DbConfiguration**の[コード ベースの構成](code-based.md)します。  

内のアセンブリ修飾型名を指定する DbConfiguration 型を設定する、 **codeConfigurationType**要素。  

> [!NOTE]
> アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。 必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF データベース プロバイダー (EF6 以降)  

EF6 では、前にデータベース プロバイダーの Entity Framework に固有の部分は、コアの ADO.NET プロバイダーの一部として含まれる必要があります。 EF6 から始めて、EF の特定の部分ようになりました管理され、個別に登録します。  

通常は、自分でプロバイダーを登録する必要はありません。 これは通常実行されます、プロバイダーをインストールするときにします。  

含めることでプロバイダーが登録されている、**プロバイダー**の下の要素、**プロバイダー**子セクションの**entityFramework**セクション。 プロバイダーのエントリに必要な 2 つの属性があります。  

- **invariantName**コア ADO.NET プロバイダーを識別しますこのプロバイダーが対象と EF。  
- **型**EF プロバイダーの実装のアセンブリ修飾型名には  

> [!NOTE]
> アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。 必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。  

例としては、次に Entity Framework をインストールするときに、既定の SQL Server プロバイダーを登録するために作成するエントリを示します。  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>インターセプター (EF6.1 以降)  

EF6.1 で始まると、インターセプターが構成ファイルに登録できます。 インターセプターを使用すると、EF は接続などを開く、データベース クエリの実行など、特定の操作を実行するときに、追加のロジックを実行できます。  

インターセプターが含めることによって登録されている、**インターセプター**の下の要素、**インターセプター**子セクションの**entityFramework**セクション。 たとえば、次の構成に登録組み込み**DatabaseLogger**インターセプターをコンソールにすべてのデータベース操作が記録されます。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>ファイル (EF6.1 以降) にデータベース操作のログ記録  

インターセプター構成ファイルを使用して登録すると、問題をデバッグする際に既存のアプリケーションにログ記録を追加するときに便利です。 **DatabaseLogger**コンストラクター パラメーターとしてファイル名を指定することで、ファイルへのログ記録をサポートしています。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

既定では、アプリが起動されるたびに新しいファイルで上書きするログ ファイルは、なります。 代わりに、ログに追加するには、ファイルが既に存在する場合などを使用します。  

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

に関する追加情報について**DatabaseLogger**インターセプターを登録するには、ブログの投稿を参照してくださいと[EF 6.1: 再コンパイルせずにログ記録をオン](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)します。  

## <a name="code-first-default-connection-factory"></a>最初の既定の接続ファクトリ コード  

構成セクションでは、Code First がコンテキストを使用するデータベースを検索に使用する既定の接続ファクトリを指定できます。 既定の接続ファクトリは、コンテキストの構成ファイルに接続文字列が追加されていない場合にのみ使用されます。  

EF の NuGet パッケージをインストールしたときに SQL Express または LocalDB は、インストールしたかに応じてのいずれかを示す既定の接続ファクトリが登録されました。  

内のアセンブリ修飾型名を指定する接続ファクトリを設定する、 **defaultConnectionFactory**要素。  

> [!NOTE]
> アセンブリの修飾名は、その後のコンマに格納されている、型、アセンブリ、名前空間修飾名です。 必要に応じてことができますもアセンブリのバージョン、カルチャ、および公開キー トークンを指定します。  

独自の既定の接続ファクトリを設定する例を次に示します。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

上記の例では、パラメーターなしのコンストラクターを持つカスタムのファクトリが必要です。 使用してコンストラクターのパラメーターを指定できますが必要な場合、**パラメーター**要素。  

たとえば、Entity Framework に含まれていると、SqlCeConnectionFactory コンストラクターにプロバイダーの不変名を指定する必要があります。 プロバイダーの不変名では、SQL Compact を使用するのバージョンを識別します。 次の構成は、既定ではバージョン 4.0 SQL Compact を使用するコンテキストになります。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

既定の接続ファクトリを設定していない場合は、Code First を使用して指す、SqlConnectionFactory`.\SQLEXPRESS`します。 SqlConnectionFactory では、接続文字列の各部をオーバーライドできるようにするコンストラクターもあります。 以外の SQL Server インスタンスを使用する場合`.\SQLEXPRESS`このコンストラクターを使用してサーバーを設定することができます。  

Code First を使用すると、次の構成、 **MyDatabaseServer**コンテキスト設定明示的な接続文字列がないです。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

既定では、コンストラクターの引数が文字列型ことが前提です。 型の属性を使用して、これを変更することができます。  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>データベースの初期化子  

データベースの初期化子は、コンテキストごとに構成されます。 構成ファイルを使用して設定できる、**コンテキスト**要素。 この要素は、構成されているコンテキストを識別するために、アセンブリ修飾名を使用します。  

既定では、コンテキストが Code First は CreateDatabaseIfNotExists 初期化子を使用して構成されます。 **DisableDatabaseInitialization**属性を**コンテキスト**データベースの初期化を無効にするために使用する要素。  

たとえば、次の構成では、MyAssembly.dll で定義されている Blogging.BlogContext コンテキストのデータベースの初期化を無効にします。  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

使用することができます、 **databaseInitializer**カスタム初期化子を設定する要素。  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

コンストラクターのパラメーターは、既定の接続ファクトリとして同じ構文を使用します。  

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

Entity Framework に含まれている汎用データベース初期化子のいずれかを構成することができます。 **型**属性はジェネリック型の .NET Framework 形式を使用します。  

たとえば、Code First Migrations を使用している場合は、使用して自動的に移行するデータベースを構成できます、`MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`初期化子。  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
