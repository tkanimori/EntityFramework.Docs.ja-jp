---
title: 接続文字列やモデル - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
caps.latest.revision: 3
ms.openlocfilehash: ca597e68a5b3e2085612669ee81da10ba6969eeb
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122759"
---
# <a name="connection-strings-and-models"></a>接続文字列とモデル
このトピックでは、Entity Framework が使用するにはどのデータベース接続を検出する方法と、それを変更する方法について説明します。 このトピックでは、Code First と EF Designer で作成されたモデル両方が説明します。  

通常、Entity Framework アプリケーションは、DbContext から派生したクラスを使用します。 この派生クラスを呼び出し、コンス トラクターのいずれかのコントロールに基本の DbContext クラスに。  

- コンテキストがデータベースに接続する方法: これはどのように接続文字列がある検出/使用済み  
- Code First を使用して、モデルの計算コンテキストを使用するかどうか、または EF Designer で作成したモデルを読み込む  
- その他の高度なオプション  

次のフラグメントの表示方法 DbContext コンス トラクターのいくつか使用できます。  

## <a name="use-code-first-with-connection-by-convention"></a>慣例により接続で、Code First を使用します。  

場合は、アプリケーションでは、その他の構成を実行して、DbContext でパラメーターなしのコンス トラクターを呼び出すと規則によって作成されたデータベース接続で Code First モードで実行する DbContext が発生します。 例えば:  

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

この例では、DbContext は、派生コンテキスト class—Demo.EF.BloggingContext—as データベース名の名前空間の修飾名を使用し、SQL Express または LocalDB を使用してこのデータベースの接続文字列を作成します。 両方がインストールされている場合は、SQL Express が使用されます。  

Visual Studio 2010 では、既定で Visual Studio 2012 および SQL Express が含まれます、後で LocalDB が含まれます。 インストール中に、データベース サーバーが使用可能な EntityFramework NuGet パッケージを確認します。 NuGet パッケージは、慣例により、接続を作成するときに Code First を使用する既定のデータベース サーバーを設定して、構成ファイルが更新されます。 SQL Express を実行している場合は、それが使用されます。 SQL Express が使用できない場合、LocalDB として登録する既定の代わりにします。 既定の接続ファクトリの設定が含まれている場合、構成ファイルは変更されません。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>規則と指定したデータベース名で接続で、Code First を使用します。  

DbContext に使用するデータベースの名前文字列コンス トラクターを呼び出すと、DbContext のデータベースに規約によって作成されたデータベース接続で Code First モードで実行は、アプリケーションでその他の構成を行っていない場合その名前。 例えば:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

この例では、DbContext は、データベース名として"BloggingDatabase"を使用し、SQL Express (Visual Studio 2010 と共にインストールされた) または LocalDB (Visual Studio 2012 と共にインストールされた) のいずれかを使用してこのデータベースの接続文字列を作成します。 両方がインストールされている場合は、SQL Express が使用されます。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>App.config/web.config ファイル内の接続文字列で Code First を使用します。  

接続文字列を app.config または web.config ファイルに配置することができます。 例えば:  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

これは、SQL Express または LocalDB 以外のデータベース サーバーを使用する DbContext を通知する簡単な方法: 上記の例は、SQL Server Compact Edition のデータベースを指定します。  

接続文字列の名前 (いずれかまたは名前空間の修飾なし) のコンテキストの名前に一致する場合、それが見つかる DbContext パラメーターなしのコンス トラクターを使用する場合。 接続文字列名が、コンテキストの名前と異なる場合は、接続文字列名を DbContext コンス トラクターに渡すことによって、Code First モードでこの接続を使用する DbContext を判断できます。 例えば:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

形式を使用する代わりに、"名前 =\<接続文字列名\>"DbContext コンス トラクターに渡される文字列。 例えば:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

このフォームでは、明示的に、構成ファイルに含まれる接続文字列を予期したとおりです。 指定した名前の接続文字列が見つからない場合、例外がスローされます。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>データベースまたは Model First app.config/web.config ファイル内の接続文字列を  

EF Designer で作成されたモデルは Code First をモデルが既に存在し、アプリケーションの実行時にコードからは生成されません。 モデルは、通常、プロジェクトで EDMX ファイルとして存在します。  

デザイナーでは、EF の接続文字列を app.config または web.config ファイルに追加します。 この接続文字列は、EDMX ファイルの情報を検索する方法に関する情報を持つという点で特殊です。 例えば:  

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

EF Designer では、接続文字列名を DbContext コンス トラクターに渡すことによってこの接続を使用する DbContext を指示するコードも生成されます。 例えば:  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

(Code First を使用して、コードから自動的に計算されるのではなく) 既存のモデルの読み込みに DbContext を知っているため、接続文字列が使用するモデルの詳細を含む EF 接続文字列。  

## <a name="other-dbcontext-constructor-options"></a>その他の DbContext コンス トラクターのオプション  

DbContext クラスには、その他のコンス トラクターとより高度なシナリオを有効にする使用パターンが含まれています。 これらのいくつか示します。  

- DbModelBuilder クラスを使用して、DbContext インスタンスをインスタンス化しなくても Code First モデルを構築することができます。 この結果は、DbModel オブジェクトです。 DbContext インスタンスを作成する準備ができたら、DbContext コンス トラクターのいずれかにこの DbModel オブジェクトを渡すことができます。  
- データベースまたは接続文字列名だけでなく、完全な接続文字列を DbContext に渡すことができます。 既定でこの接続文字列は System.Data.SqlClient プロバイダーの使用します。これは、コンテキストに IConnectionFactory の別の実装を設定して変更できます。Database.DefaultConnectionFactory します。  
- DbContext コンス トラクターに渡すことによって、既存の DbConnection オブジェクトを使用できます。 計算するのではなく、使用される接続で指定されたモデルになる接続オブジェクトが、EntityConnection のインスタンスの場合は、まずコードを使用してモデル。 かどうか、オブジェクトには他の型のインスタンス: SqlConnection など、コンテキストが Code First モードで使用されます。  
- 既存のコンテキストをラップする DbContext を作成する DbContext コンス トラクターに既存の ObjectContext を渡すことができます。 これは、ObjectContext を使用するが、アプリケーションの一部の DbContext を活用するためにすると、既存のアプリケーションに対して使用できます。  
