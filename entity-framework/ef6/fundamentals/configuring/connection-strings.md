---
title: 接続文字列やモデル - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
ms.openlocfilehash: 2c9f084107e4de7f5439bf0082b46a3b538496e0
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490747"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="2dfdc-102">接続文字列とモデル</span><span class="sxs-lookup"><span data-stu-id="2dfdc-102">Connection strings and models</span></span>
<span data-ttu-id="2dfdc-103">このトピックでは、Entity Framework が使用するにはどのデータベース接続を検出する方法と、それを変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-103">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="2dfdc-104">このトピックでは、Code First と EF Designer で作成されたモデル両方が説明します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-104">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="2dfdc-105">通常、Entity Framework アプリケーションは、DbContext から派生したクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-105">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="2dfdc-106">この派生クラスを呼び出し、コンストラクターのいずれかのコントロールに基本の DbContext クラスに。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-106">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="2dfdc-107">コンテキストがデータベースに接続する方法: これはどのように接続文字列がある検出/使用済み</span><span class="sxs-lookup"><span data-stu-id="2dfdc-107">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="2dfdc-108">Code First を使用して、モデルの計算コンテキストを使用するかどうか、または EF Designer で作成したモデルを読み込む</span><span class="sxs-lookup"><span data-stu-id="2dfdc-108">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="2dfdc-109">その他の高度なオプション</span><span class="sxs-lookup"><span data-stu-id="2dfdc-109">Additional advanced options</span></span>  

<span data-ttu-id="2dfdc-110">次のフラグメントの表示方法 DbContext コンストラクターのいくつか使用できます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-110">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="2dfdc-111">慣例により接続で、Code First を使用します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-111">Use Code First with connection by convention</span></span>  

<span data-ttu-id="2dfdc-112">場合は、アプリケーションでは、その他の構成を実行して、DbContext でパラメーターなしのコンストラクターを呼び出すと規則によって作成されたデータベース接続で Code First モードで実行する DbContext が発生します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-112">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="2dfdc-113">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-113">For example:</span></span>  

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

<span data-ttu-id="2dfdc-114">この例では、DbContext は、派生コンテキスト class—Demo.EF.BloggingContext—as データベース名の名前空間の修飾名を使用し、SQL Express または LocalDB を使用してこのデータベースの接続文字列を作成します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-114">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="2dfdc-115">両方がインストールされている場合は、SQL Express が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-115">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="2dfdc-116">Visual Studio 2010 では、既定で Visual Studio 2012 および SQL Express が含まれます、後で LocalDB が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-116">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="2dfdc-117">インストール中に、データベース サーバーが使用可能な EntityFramework NuGet パッケージを確認します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-117">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="2dfdc-118">NuGet パッケージは、慣例により、接続を作成するときに Code First を使用する既定のデータベース サーバーを設定して、構成ファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-118">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="2dfdc-119">SQL Express を実行している場合は、それが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-119">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="2dfdc-120">SQL Express が使用できない場合、LocalDB として登録する既定の代わりにします。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-120">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="2dfdc-121">既定の接続ファクトリの設定が含まれている場合、構成ファイルは変更されません。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-121">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="2dfdc-122">規則と指定したデータベース名で接続で、Code First を使用します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-122">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="2dfdc-123">DbContext に使用するデータベースの名前文字列コンストラクターを呼び出すと、DbContext のデータベースに規約によって作成されたデータベース接続で Code First モードで実行は、アプリケーションでその他の構成を行っていない場合その名前。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-123">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="2dfdc-124">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-124">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="2dfdc-125">この例では、DbContext は、データベース名として"BloggingDatabase"を使用し、SQL Express (Visual Studio 2010 と共にインストールされた) または LocalDB (Visual Studio 2012 と共にインストールされた) のいずれかを使用してこのデータベースの接続文字列を作成します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-125">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="2dfdc-126">両方がインストールされている場合は、SQL Express が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-126">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="2dfdc-127">App.config/web.config ファイル内の接続文字列で Code First を使用します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-127">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="2dfdc-128">接続文字列を app.config または web.config ファイルに配置することができます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-128">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="2dfdc-129">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-129">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="2dfdc-130">これは、SQL Express または LocalDB 以外のデータベース サーバーを使用する DbContext を通知する簡単な方法: 上記の例は、SQL Server Compact Edition のデータベースを指定します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-130">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="2dfdc-131">接続文字列の名前 (いずれかまたは名前空間の修飾なし) のコンテキストの名前に一致する場合、それが見つかる DbContext パラメーターなしのコンストラクターを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-131">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="2dfdc-132">接続文字列名が、コンテキストの名前と異なる場合は、接続文字列名を DbContext コンストラクターに渡すことによって、Code First モードでこの接続を使用する DbContext を判断できます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-132">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="2dfdc-133">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-133">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="2dfdc-134">形式を使用する代わりに、"名前 =\<接続文字列名\>"DbContext コンストラクターに渡される文字列。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-134">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="2dfdc-135">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-135">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="2dfdc-136">このフォームでは、明示的に、構成ファイルに含まれる接続文字列を予期したとおりです。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-136">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="2dfdc-137">指定した名前の接続文字列が見つからない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-137">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="2dfdc-138">データベースまたは Model First app.config/web.config ファイル内の接続文字列を</span><span class="sxs-lookup"><span data-stu-id="2dfdc-138">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="2dfdc-139">EF Designer で作成されたモデルは Code First をモデルが既に存在し、アプリケーションの実行時にコードからは生成されません。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-139">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="2dfdc-140">モデルは、通常、プロジェクトで EDMX ファイルとして存在します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-140">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="2dfdc-141">デザイナーでは、EF の接続文字列を app.config または web.config ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-141">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="2dfdc-142">この接続文字列は、EDMX ファイルの情報を検索する方法に関する情報を持つという点で特殊です。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-142">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="2dfdc-143">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-143">For example:</span></span>  

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

<span data-ttu-id="2dfdc-144">EF Designer では、接続文字列名を DbContext コンストラクターに渡すことによってこの接続を使用する DbContext を指示するコードも生成されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-144">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="2dfdc-145">例えば:</span><span class="sxs-lookup"><span data-stu-id="2dfdc-145">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="2dfdc-146">(Code First を使用して、コードから自動的に計算されるのではなく) 既存のモデルの読み込みに DbContext を知っているため、接続文字列が使用するモデルの詳細を含む EF 接続文字列。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-146">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="2dfdc-147">その他の DbContext コンストラクターのオプション</span><span class="sxs-lookup"><span data-stu-id="2dfdc-147">Other DbContext constructor options</span></span>  

<span data-ttu-id="2dfdc-148">DbContext クラスには、その他のコンストラクターとより高度なシナリオを有効にする使用パターンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-148">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="2dfdc-149">これらのいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-149">Some of these are:</span></span>  

- <span data-ttu-id="2dfdc-150">DbModelBuilder クラスを使用して、DbContext インスタンスをインスタンス化しなくても Code First モデルを構築することができます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-150">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="2dfdc-151">この結果は、DbModel オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-151">The result of this is a DbModel object.</span></span> <span data-ttu-id="2dfdc-152">DbContext インスタンスを作成する準備ができたら、DbContext コンストラクターのいずれかにこの DbModel オブジェクトを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-152">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="2dfdc-153">データベースまたは接続文字列名だけでなく、完全な接続文字列を DbContext に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-153">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="2dfdc-154">既定でこの接続文字列は System.Data.SqlClient プロバイダーの使用します。これは、コンテキストに IConnectionFactory の別の実装を設定して変更できます。Database.DefaultConnectionFactory します。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-154">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="2dfdc-155">DbContext コンストラクターに渡すことによって、既存の DbConnection オブジェクトを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-155">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="2dfdc-156">計算するのではなく、使用される接続で指定されたモデルになる接続オブジェクトが、EntityConnection のインスタンスの場合は、まずコードを使用してモデル。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-156">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="2dfdc-157">かどうか、オブジェクトには他の型のインスタンス: SqlConnection など、コンテキストが Code First モードで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-157">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="2dfdc-158">既存のコンテキストをラップする DbContext を作成する DbContext コンストラクターに既存の ObjectContext を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-158">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="2dfdc-159">これは、ObjectContext を使用するが、アプリケーションの一部の DbContext を活用するためにすると、既存のアプリケーションに対して使用できます。</span><span class="sxs-lookup"><span data-stu-id="2dfdc-159">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
