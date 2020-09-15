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
# <a name="connection-strings-and-models"></a><span data-ttu-id="7aa4b-103">接続文字列とモデル</span><span class="sxs-lookup"><span data-stu-id="7aa4b-103">Connection strings and models</span></span>
<span data-ttu-id="7aa4b-104">このトピックでは、Entity Framework 使用するデータベース接続とその変更方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="7aa4b-105">Code First と EF デザイナーで作成されたモデルについては、このトピックで説明します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="7aa4b-106">通常、Entity Framework アプリケーションは、DbContext から派生したクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="7aa4b-107">この派生クラスは、基本の DbContext クラスのコンストラクターの1つを呼び出して、次の操作を制御します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="7aa4b-108">コンテキストがデータベースに接続する方法 (接続文字列の検出/使用方法)</span><span class="sxs-lookup"><span data-stu-id="7aa4b-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="7aa4b-109">コンテキストで Code First を使用したモデルの計算を使用するか、EF デザイナーで作成されたモデルを読み込むか</span><span class="sxs-lookup"><span data-stu-id="7aa4b-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="7aa4b-110">その他の詳細オプション</span><span class="sxs-lookup"><span data-stu-id="7aa4b-110">Additional advanced options</span></span>  

<span data-ttu-id="7aa4b-111">次のフラグメントは、DbContext コンストラクターを使用するいくつかの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="7aa4b-112">規則による接続で Code First を使用する</span><span class="sxs-lookup"><span data-stu-id="7aa4b-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="7aa4b-113">アプリケーションで他の構成を実行していない場合は、DbContext でパラメーターなしのコンストラクターを呼び出すと、規則によって作成されたデータベース接続を使用して、DbContext が Code First モードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="7aa4b-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-114">For example:</span></span>  

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

<span data-ttu-id="7aa4b-115">この例では、DbContext は、派生コンテキストクラスの名前空間修飾名 (Demo. EF. Bのコンテキスト) をデータベース名として使用し、SQL Express または LocalDB を使用してこのデータベースの接続文字列を作成します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="7aa4b-116">両方がインストールされている場合は、SQL Express が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="7aa4b-117">Visual Studio 2010 には、既定では SQL Express が含まれており、Visual Studio 2012 以降には LocalDB が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="7aa4b-118">インストール中に、使用可能なデータベースサーバーが EntityFramework NuGet パッケージによって確認されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="7aa4b-119">次に NuGet パッケージは、規則に従って接続を作成するときに Code First 使用する既定のデータベースサーバーを設定して、構成ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="7aa4b-120">SQL Express が実行されている場合は、それが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="7aa4b-121">SQL Express を使用できない場合は、LocalDB が既定値として登録されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="7aa4b-122">既定の接続ファクトリの設定が既に含まれている場合、構成ファイルに変更は加えられません。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="7aa4b-123">規則による接続とデータベース名を指定して Code First を使用する</span><span class="sxs-lookup"><span data-stu-id="7aa4b-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="7aa4b-124">アプリケーションで他の構成を実行していない場合は、使用するデータベース名を指定して DbContext の文字列コンストラクターを呼び出すと、その名前のデータベースに対して規則によって作成されたデータベース接続を使用して、DbContext が Code First モードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="7aa4b-125">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="7aa4b-126">この例では、DbContext はデータベース名として "Bのデータベース名" を使用し、このデータベースの接続文字列を作成します (Visual Studio 2010 と共にインストールされた) か、LocalDB (Visual Studio 2012 と共にインストールされます) を使用します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="7aa4b-127">両方がインストールされている場合は、SQL Express が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="7aa4b-128">app.config/web.config ファイルで接続文字列を使用して Code First を使用する</span><span class="sxs-lookup"><span data-stu-id="7aa4b-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="7aa4b-129">app.config または web.config ファイルに接続文字列を配置することもできます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="7aa4b-130">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="7aa4b-131">これは、SQL Express または LocalDB 以外のデータベースサーバーを使用するように DbContext に指示する簡単な方法です。上記の例では、SQL Server Compact Edition データベースを指定しています。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="7aa4b-132">接続文字列の名前がコンテキストの名前と一致する場合 (名前空間の修飾子があるかどうかに関係なく)、パラメーターなしのコンストラクターが使用されるときに DbContext によって検索されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="7aa4b-133">接続文字列名がコンテキストの名前と異なる場合は、接続文字列名を DbContext コンストラクターに渡すことによって、Code First モードでこの接続を使用するように DbContext に指示できます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="7aa4b-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="7aa4b-135">または、 \<connection string name\> DbContext コンストラクターに渡される文字列に "name =" という形式を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="7aa4b-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="7aa4b-137">この形式を使用すると、構成ファイルに接続文字列が見つかると想定されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="7aa4b-138">指定した名前の接続文字列が見つからない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="7aa4b-139">app.config/web.config ファイルの接続文字列を使用したデータベース/Model First</span><span class="sxs-lookup"><span data-stu-id="7aa4b-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="7aa4b-140">EF デザイナーで作成されたモデルは、モデルが既に存在していて、アプリケーションの実行時にコードから生成されないという Code First とは異なります。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="7aa4b-141">モデルは通常、プロジェクト内の EDMX ファイルとして存在します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="7aa4b-142">デザイナーは、app.config または web.config ファイルに EF 接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="7aa4b-143">この接続文字列は、EDMX ファイル内の情報を検索する方法に関する情報が含まれていることに特に注意してください。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="7aa4b-144">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-144">For example:</span></span>  

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

<span data-ttu-id="7aa4b-145">また、EF デザイナーは、接続文字列名を DbContext コンストラクターに渡すことによって、この接続を使用するように DbContext に指示するコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="7aa4b-146">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="7aa4b-147">DbContext は、(Code First を使用してコードから計算するのではなく) 既存のモデルを読み込むことを認識します。接続文字列は、使用するモデルの詳細を含む EF 接続文字列であるためです。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="7aa4b-148">その他の DbContext コンストラクターオプション</span><span class="sxs-lookup"><span data-stu-id="7aa4b-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="7aa4b-149">DbContext クラスには、さらに高度なシナリオを可能にする他のコンストラクターと使用パターンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="7aa4b-150">それらの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-150">Some of these are:</span></span>  

- <span data-ttu-id="7aa4b-151">DbModelBuilder クラスを使用すると、DbContext インスタンスをインスタンス化せずに Code First モデルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="7aa4b-152">この結果、DbModel オブジェクトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="7aa4b-153">Dbmodel インスタンスを作成する準備ができたら、この DbModel オブジェクトを Dbmodel コンストラクターのいずれかに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="7aa4b-154">データベース名または接続文字列名だけでなく、完全な接続文字列を DbContext に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="7aa4b-155">既定では、この接続文字列は、system.string プロバイダーで使用されます。これは、IConnectionFactory の別の実装をコンテキストに設定することによって変更できます。データベース. DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="7aa4b-156">DbContext コンストラクターに渡すことによって、既存の DbConnection オブジェクトを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="7aa4b-157">接続オブジェクトが EntityConnection のインスタンスである場合は、Code First を使用してモデルを計算するのではなく、接続で指定されたモデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="7aa4b-158">オブジェクトが他の型のインスタンス (SqlConnection など) の場合、コンテキストはそれを Code First モードで使用します。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="7aa4b-159">既存の ObjectContext を DbContext コンストラクターに渡して、既存のコンテキストをラップする DbContext を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="7aa4b-160">これは、ObjectContext を使用するが、アプリケーションの一部で DbContext を利用する既存のアプリケーションに使用できます。</span><span class="sxs-lookup"><span data-stu-id="7aa4b-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
