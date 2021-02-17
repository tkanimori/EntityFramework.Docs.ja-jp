---
title: コードベースの構成-EF6
description: Entity Framework 6 のコードベースの構成
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: b16cbcef85708730dcc6b82a38635cc60cb2206a
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543173"
---
# <a name="code-based-configuration"></a><span data-ttu-id="e7cd9-103">コードベースの構成</span><span class="sxs-lookup"><span data-stu-id="e7cd9-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="e7cd9-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e7cd9-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e7cd9-106">Entity Framework アプリケーションの構成は、構成ファイル (app.config/web.config) またはコードを使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="e7cd9-107">後者は、コードベースの構成と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="e7cd9-108">構成ファイルの構成については、 [別の記事](xref:ef6/fundamentals/configuring/config-file)で説明します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="e7cd9-109">構成ファイルは、コードベースの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="e7cd9-110">つまり、構成オプションがコードと構成ファイルの両方で設定されている場合、構成ファイルの設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="e7cd9-111">`DbConfiguration` の使用</span><span class="sxs-lookup"><span data-stu-id="e7cd9-111">Using `DbConfiguration`</span></span>

<span data-ttu-id="e7cd9-112">EF6 以降のコードベースの構成は、のサブクラスを作成することによって実現され `System.Data.Entity.Config.DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-112">Code-based configuration in EF6 and above is achieved by creating a subclass of `System.Data.Entity.Config.DbConfiguration`.</span></span> <span data-ttu-id="e7cd9-113">サブクラス化する場合は、次のガイドラインに従う必要があり `DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-113">The following guidelines should be followed when subclassing `DbConfiguration`:</span></span>  

- <span data-ttu-id="e7cd9-114">`DbConfiguration`アプリケーションのクラスを1つだけ作成します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-114">Create only one `DbConfiguration` class for your application.</span></span> <span data-ttu-id="e7cd9-115">このクラスは、アプリドメイン全体の設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="e7cd9-116">クラス `DbConfiguration` をクラスと同じアセンブリに配置し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-116">Place your `DbConfiguration` class in the same assembly as your `DbContext` class.</span></span> <span data-ttu-id="e7cd9-117">(これを変更する場合は、「*移動 `DbConfiguration`* 」セクションを参照してください)。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-117">(See the *Moving `DbConfiguration`* section if you want to change this.)</span></span>  
- <span data-ttu-id="e7cd9-118">クラスに `DbConfiguration` パブリックなパラメーターなしのコンストラクターを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-118">Give your `DbConfiguration` class a public parameterless constructor.</span></span>  
- <span data-ttu-id="e7cd9-119">このコンストラクター内から保護されたメソッドを呼び出すことによって、構成オプションを設定 `DbConfiguration` します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-119">Set configuration options by calling protected `DbConfiguration` methods from within this constructor.</span></span>  

<span data-ttu-id="e7cd9-120">これらのガイドラインに従うことで、EF は、モデルにアクセスする必要があるツールとアプリケーションの実行時の両方で、構成を自動的に検出して使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="e7cd9-121">例</span><span class="sxs-lookup"><span data-stu-id="e7cd9-121">Example</span></span>  

<span data-ttu-id="e7cd9-122">から派生したクラスは次の `DbConfiguration` ようになります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-122">A class derived from `DbConfiguration` might look like this:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

<span data-ttu-id="e7cd9-123">このクラスは、SQL Azure の実行戦略を使用するように EF を設定します。これにより、失敗したデータベース操作を自動的に再試行し、Code First から規則によって作成されたデータベースに対してローカル DB を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="e7cd9-124">動き `DbConfiguration`</span><span class="sxs-lookup"><span data-stu-id="e7cd9-124">Moving `DbConfiguration`</span></span>  

<span data-ttu-id="e7cd9-125">クラスと同じアセンブリにクラスを配置できない場合があり `DbConfiguration` `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-125">There are cases where it is not possible to place your `DbConfiguration` class in the same assembly as your `DbContext` class.</span></span> <span data-ttu-id="e7cd9-126">たとえば、2つのクラスが `DbContext` それぞれ異なるアセンブリに存在する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-126">For example, you may have two `DbContext` classes each in different assemblies.</span></span> <span data-ttu-id="e7cd9-127">この処理には、2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-127">There are two options for handling this.</span></span>  

<span data-ttu-id="e7cd9-128">最初のオプションでは、構成ファイルを使用して、 `DbConfiguration` 使用するインスタンスを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-128">The first option is to use the config file to specify the `DbConfiguration` instance to use.</span></span> <span data-ttu-id="e7cd9-129">これを行うには、entityFramework セクションの codeConfigurationType 属性を設定します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="e7cd9-130">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="e7cd9-131">CodeConfigurationType の値は、クラスのアセンブリおよび名前空間修飾名である必要があり `DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your `DbConfiguration` class.</span></span>  

<span data-ttu-id="e7cd9-132">2番目のオプションは、コンテキストクラスに配置することです `DbConfigurationTypeAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-132">The second option is to place `DbConfigurationTypeAttribute` on your context class.</span></span> <span data-ttu-id="e7cd9-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="e7cd9-134">属性に渡される値は、 `DbConfiguration` 上に示すように型にするか、アセンブリと名前空間で修飾された型名の文字列にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-134">The value passed to the attribute can either be your `DbConfiguration` type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="e7cd9-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="e7cd9-136">設定 ( `DbConfiguration` 明示的に)</span><span class="sxs-lookup"><span data-stu-id="e7cd9-136">Setting `DbConfiguration` explicitly</span></span>  

<span data-ttu-id="e7cd9-137">型を使用する前に構成が必要になる状況もあり `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-137">There are some situations where configuration may be needed before any `DbContext` type has been used.</span></span> <span data-ttu-id="e7cd9-138">この例には以下のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-138">Examples of this include:</span></span>  

- <span data-ttu-id="e7cd9-139">を使用して `DbModelBuilder` コンテキストなしでモデルを構築する</span><span class="sxs-lookup"><span data-stu-id="e7cd9-139">Using `DbModelBuilder` to build a model without a context</span></span>  
- <span data-ttu-id="e7cd9-140">`DbContext`アプリケーションコンテキストが使用される前にコンテキストが使用されるを利用する他のフレームワーク/ユーティリティコードを使用する</span><span class="sxs-lookup"><span data-stu-id="e7cd9-140">Using some other framework/utility code that utilizes a `DbContext` where that context is used before your application context is used</span></span>  

<span data-ttu-id="e7cd9-141">このような状況では、EF は構成を自動的に検出できないため、代わりに次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="e7cd9-142">上の `DbConfiguration` 「 *`DbConfiguration` 移動*」セクションで説明したように、構成ファイルで型を設定します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-142">Set the `DbConfiguration` type in the config file, as described in the *Moving `DbConfiguration`* section above</span></span>
- <span data-ttu-id="e7cd9-143">静的を呼び出し `DbConfiguration` ます。アプリケーションの起動中の SetConfiguration メソッド</span><span class="sxs-lookup"><span data-stu-id="e7cd9-143">Call the static `DbConfiguration`.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="e7cd9-144">オーバーライド `DbConfiguration`</span><span class="sxs-lookup"><span data-stu-id="e7cd9-144">Overriding `DbConfiguration`</span></span>  

<span data-ttu-id="e7cd9-145">で構成セットを上書きする必要がある状況もあり `DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-145">There are some situations where you need to override the configuration set in the `DbConfiguration`.</span></span> <span data-ttu-id="e7cd9-146">これは通常、アプリケーション開発者によって行われるのではなく、派生クラスを使用できないサードパーティプロバイダーやプラグインによって実行され `DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived `DbConfiguration` class.</span></span>  

<span data-ttu-id="e7cd9-147">このため、EntityFramework では、ロックダウンの直前に既存の構成を変更できるイベントハンドラーを登録できます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="e7cd9-148">また、EF サービスロケーターによって返されるサービスを置き換えるための、砂糖の方法も提供します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="e7cd9-149">これは、次のように使用することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="e7cd9-150">(EF が使用される前に) アプリの起動時に、プラグインまたはプロバイダーがこのイベントのイベントハンドラーメソッドを登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="e7cd9-151">(これは、アプリケーションで EF を使用する前に行う必要があることに注意してください)。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="e7cd9-152">イベントハンドラーは、置き換える必要があるすべてのサービスに対して、交換用 Eservice を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="e7cd9-153">たとえば、を置き換えるには、次の `IDbConnectionFactory` `DbProviderService` ようにハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-153">For example, to replace `IDbConnectionFactory` and `DbProviderService` you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="e7cd9-154">上記のコードでは、 `MyProviderServices` とは `MyConnectionFactory` サービスの実装を表しています。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-154">In the code above, `MyProviderServices` and `MyConnectionFactory` represent your implementations of the service.</span></span>  

<span data-ttu-id="e7cd9-155">また、依存関係ハンドラーを追加して同じ効果を得ることもできます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="e7cd9-156">この方法でラップすることもでき `DbProviderFactory` ますが、これを行うと ef にのみ影響し、 `DbProviderFactory` ef の外部では使用されません。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-156">Note that you could also wrap `DbProviderFactory` in this way, but doing so will only affect EF and not uses of the `DbProviderFactory` outside of EF.</span></span> <span data-ttu-id="e7cd9-157">このため、以前と同じように、引き続きラップする必要があり `DbProviderFactory` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-157">For this reason you’ll probably want to continue to wrap `DbProviderFactory` as you have before.</span></span>  

<span data-ttu-id="e7cd9-158">また、パッケージマネージャーコンソールから移行を実行する場合など、アプリケーションの外部で実行するサービスについても考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="e7cd9-159">コンソールから移行を実行すると、の検索が試行され `DbConfiguration` ます。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-159">When you run migrate from the console it will attempt to find your `DbConfiguration`.</span></span> <span data-ttu-id="e7cd9-160">ただし、ラップされたサービスを取得するかどうかは、イベントハンドラーの登録場所によって異なります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="e7cd9-161">の構築の一部として登録されている場合 `DbConfiguration` 、コードを実行し、サービスをラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-161">If it is registered as part of the construction of your `DbConfiguration` then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="e7cd9-162">通常、これは当てはまりません。これは、ツールがラップされたサービスを取得しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="e7cd9-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
