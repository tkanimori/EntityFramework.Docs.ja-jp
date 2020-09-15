---
title: 接続文字列とモデル-EF6
description: Entity Framework 6 の接続文字列とモデル
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: 45db461b18cde3bc1f1fccadec3c8ece6dd16832
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070784"
---
# <a name="connection-strings-and-models"></a>接続文字列とモデル
このトピックでは、Entity Framework 使用するデータベース接続とその変更方法について説明します。 Code First と EF デザイナーで作成されたモデルについては、このトピックで説明します。  

通常、Entity Framework アプリケーションは、DbContext から派生したクラスを使用します。 この派生クラスは、基本の DbContext クラスのコンストラクターの1つを呼び出して、次の操作を制御します。  

- コンテキストがデータベースに接続する方法 (接続文字列の検出/使用方法)  
- コンテキストで Code First を使用したモデルの計算を使用するか、EF デザイナーで作成されたモデルを読み込むか  
- その他の詳細オプション  

次のフラグメントは、DbContext コンストラクターを使用するいくつかの方法を示しています。  

## <a name="use-code-first-with-connection-by-convention"></a>規則による接続で Code First を使用する  

アプリケーションで他の構成を実行していない場合は、DbContext でパラメーターなしのコンストラクターを呼び出すと、規則によって作成されたデータベース接続を使用して、DbContext が Code First モードで実行されます。 次に例を示します。  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

この例では、DbContext は、派生コンテキストクラスの名前空間修飾名 (Demo. EF. Bのコンテキスト) をデータベース名として使用し、SQL Express または LocalDB を使用してこのデータベースの接続文字列を作成します。 両方がインストールされている場合は、SQL Express が使用されます。  

Visual Studio 2010 には、既定では SQL Express が含まれており、Visual Studio 2012 以降には LocalDB が含まれています。 インストール中に、使用可能なデータベースサーバーが EntityFramework NuGet パッケージによって確認されます。 次に NuGet パッケージは、規則に従って接続を作成するときに Code First 使用する既定のデータベースサーバーを設定して、構成ファイルを更新します。 SQL Express が実行されている場合は、それが使用されます。 SQL Express を使用できない場合は、LocalDB が既定値として登録されます。 既定の接続ファクトリの設定が既に含まれている場合、構成ファイルに変更は加えられません。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>規則による接続とデータベース名を指定して Code First を使用する  

アプリケーションで他の構成を実行していない場合は、使用するデータベース名を指定して DbContext の文字列コンストラクターを呼び出すと、その名前のデータベースに対して規則によって作成されたデータベース接続を使用して、DbContext が Code First モードで実行されます。 次に例を示します。  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

この例では、DbContext はデータベース名として "Bのデータベース名" を使用し、このデータベースの接続文字列を作成します (Visual Studio 2010 と共にインストールされた) か、LocalDB (Visual Studio 2012 と共にインストールされます) を使用します。 両方がインストールされている場合は、SQL Express が使用されます。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>app.config/web.config ファイルで接続文字列を使用して Code First を使用する  

app.config または web.config ファイルに接続文字列を配置することもできます。 次に例を示します。  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

これは、SQL Express または LocalDB 以外のデータベースサーバーを使用するように DbContext に指示する簡単な方法です。上記の例では、SQL Server Compact Edition データベースを指定しています。  

接続文字列の名前がコンテキストの名前と一致する場合 (名前空間の修飾子があるかどうかに関係なく)、パラメーターなしのコンストラクターが使用されるときに DbContext によって検索されます。 接続文字列名がコンテキストの名前と異なる場合は、接続文字列名を DbContext コンストラクターに渡すことによって、Code First モードでこの接続を使用するように DbContext に指示できます。 次に例を示します。  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

または、 \<connection string name\> DbContext コンストラクターに渡される文字列に "name =" という形式を使用することもできます。 次に例を示します。  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

この形式を使用すると、構成ファイルに接続文字列が見つかると想定されます。 指定した名前の接続文字列が見つからない場合、例外がスローされます。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>app.config/web.config ファイルの接続文字列を使用したデータベース/Model First  

EF デザイナーで作成されたモデルは、モデルが既に存在していて、アプリケーションの実行時にコードから生成されないという Code First とは異なります。 モデルは通常、プロジェクト内の EDMX ファイルとして存在します。  

デザイナーは、app.config または web.config ファイルに EF 接続文字列を追加します。 この接続文字列は、EDMX ファイル内の情報を検索する方法に関する情報が含まれていることに特に注意してください。 次に例を示します。  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

また、EF デザイナーは、接続文字列名を DbContext コンストラクターに渡すことによって、この接続を使用するように DbContext に指示するコードを生成します。 次に例を示します。  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext は、(Code First を使用してコードから計算するのではなく) 既存のモデルを読み込むことを認識します。接続文字列は、使用するモデルの詳細を含む EF 接続文字列であるためです。  

## <a name="other-dbcontext-constructor-options"></a>その他の DbContext コンストラクターオプション  

DbContext クラスには、さらに高度なシナリオを可能にする他のコンストラクターと使用パターンが含まれています。 それらの一部を次に示します。  

- DbModelBuilder クラスを使用すると、DbContext インスタンスをインスタンス化せずに Code First モデルを作成できます。 この結果、DbModel オブジェクトが生成されます。 Dbmodel インスタンスを作成する準備ができたら、この DbModel オブジェクトを Dbmodel コンストラクターのいずれかに渡すことができます。  
- データベース名または接続文字列名だけでなく、完全な接続文字列を DbContext に渡すことができます。 既定では、この接続文字列は、system.string プロバイダーで使用されます。これは、IConnectionFactory の別の実装をコンテキストに設定することによって変更できます。データベース. DefaultConnectionFactory。  
- DbContext コンストラクターに渡すことによって、既存の DbConnection オブジェクトを使用できます。 接続オブジェクトが EntityConnection のインスタンスである場合は、Code First を使用してモデルを計算するのではなく、接続で指定されたモデルが使用されます。 オブジェクトが他の型のインスタンス (SqlConnection など) の場合、コンテキストはそれを Code First モードで使用します。  
- 既存の ObjectContext を DbContext コンストラクターに渡して、既存のコンテキストをラップする DbContext を作成することができます。 これは、ObjectContext を使用するが、アプリケーションの一部で DbContext を利用する既存のアプリケーションに使用できます。  
