---
title: コード ベースの構成 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: c317f112f713612f7b9aef3764a0bd004fef5424
ms.sourcegitcommit: 735715f10cc8a231c213e4f055d79f0effd86570
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325354"
---
# <a name="code-based-configuration"></a><span data-ttu-id="038b0-102">コード ベースの構成</span><span class="sxs-lookup"><span data-stu-id="038b0-102">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="038b0-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="038b0-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="038b0-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="038b0-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="038b0-105">構成ファイル (app.config/web.config) またはコードによって、Entity Framework アプリケーションの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="038b0-105">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="038b0-106">後者の場合は、コード ベースの構成と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="038b0-106">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="038b0-107">構成ファイル内の構成については、「、[別の記事](config-file.md)します。</span><span class="sxs-lookup"><span data-stu-id="038b0-107">Configuration in a config file is described in a [separate article](config-file.md).</span></span> <span data-ttu-id="038b0-108">構成ファイルは、コード ベースの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="038b0-108">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="038b0-109">つまり、コードの両方でと、構成ファイルで、構成オプションが設定されている場合は、構成ファイルで設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="038b0-109">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="038b0-110">DbConfiguration を使用します。</span><span class="sxs-lookup"><span data-stu-id="038b0-110">Using DbConfiguration</span></span>  

<span data-ttu-id="038b0-111">EF6 と上記のコード ベースの構成は、System.Data.Entity.Config.DbConfiguration のサブクラスを作成することによって実現されます。</span><span class="sxs-lookup"><span data-stu-id="038b0-111">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="038b0-112">次のガイドラインは、DbConfiguration をサブクラス化するとき、その後にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-112">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="038b0-113">アプリケーションの 1 つだけの DbConfiguration クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="038b0-113">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="038b0-114">このクラスには、アプリ ドメイン全体の設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="038b0-114">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="038b0-115">DbContext クラスと同じアセンブリ内の DbConfiguration クラスを配置します。</span><span class="sxs-lookup"><span data-stu-id="038b0-115">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="038b0-116">(を参照してください、*移動 DbConfiguration*セクションを変更する場合)。</span><span class="sxs-lookup"><span data-stu-id="038b0-116">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="038b0-117">DbConfiguration クラスのパブリック コンス トラクターを提供します。</span><span class="sxs-lookup"><span data-stu-id="038b0-117">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="038b0-118">このコンス トラクター内からの保護の DbConfiguration メソッドを呼び出すことによって、構成オプションを設定します。</span><span class="sxs-lookup"><span data-stu-id="038b0-118">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="038b0-119">これらのガイドラインに従うと、EF を探して、モデルにアクセスする必要があると、アプリケーションを実行するときに両方のツールで、構成を自動的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="038b0-119">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="038b0-120">例</span><span class="sxs-lookup"><span data-stu-id="038b0-120">Example</span></span>  

<span data-ttu-id="038b0-121">DbConfiguration から派生したクラスは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="038b0-121">A class derived from DbConfiguration might look like this:</span></span>  

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

<span data-ttu-id="038b0-122">このクラスは、- 失敗したデータベースの操作を自動的に再試行して、Code First の規則によって作成されるデータベースのローカル DB を使用して、SQL Azure の実行方法 - を使用する EF を設定します。</span><span class="sxs-lookup"><span data-stu-id="038b0-122">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="038b0-123">DbConfiguration の移動</span><span class="sxs-lookup"><span data-stu-id="038b0-123">Moving DbConfiguration</span></span>  

<span data-ttu-id="038b0-124">可能性がありますが、DbContext クラスと同じアセンブリ内の DbConfiguration クラスを配置することはできません。</span><span class="sxs-lookup"><span data-stu-id="038b0-124">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="038b0-125">たとえば、2 つ DbContext クラスにはそれぞれを異なるアセンブリがあります。</span><span class="sxs-lookup"><span data-stu-id="038b0-125">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="038b0-126">これを処理するための 2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="038b0-126">There are two options for handling this.</span></span>  

<span data-ttu-id="038b0-127">最初のオプションでは、使用する DbConfiguration インスタンスを指定する構成ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="038b0-127">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="038b0-128">これを行うには、entityFramework セクションの codeConfigurationType 属性を設定します。</span><span class="sxs-lookup"><span data-stu-id="038b0-128">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="038b0-129">例:</span><span class="sxs-lookup"><span data-stu-id="038b0-129">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="038b0-130">CodeConfigurationType の値は、アセンブリと DbConfiguration クラスの名前空間の修飾名である必要があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-130">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="038b0-131">2 番目のオプションは、DbConfigurationTypeAttribute をコンテキスト クラスに配置することです。</span><span class="sxs-lookup"><span data-stu-id="038b0-131">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="038b0-132">例:</span><span class="sxs-lookup"><span data-stu-id="038b0-132">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="038b0-133">使用できます、DbConfiguration 型では、上記の属性に値が渡されるか、アセンブリと名前空間の修飾型名の文字列。</span><span class="sxs-lookup"><span data-stu-id="038b0-133">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="038b0-134">例:</span><span class="sxs-lookup"><span data-stu-id="038b0-134">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="038b0-135">DbConfiguration を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="038b0-135">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="038b0-136">状況によっては、前に、任意の DbContext 型が使用されている構成を必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-136">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="038b0-137">この追加の例:</span><span class="sxs-lookup"><span data-stu-id="038b0-137">Examples of this include:</span></span>  

- <span data-ttu-id="038b0-138">DbModelBuilder を使用して、コンテキストなしのモデルを構築するには</span><span class="sxs-lookup"><span data-stu-id="038b0-138">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="038b0-139">アプリケーションのコンテキストを使用する前にそのコンテキストが使用されている DbContext を使用するその他のフレームワーク/ユーティリティ コードを使用します。</span><span class="sxs-lookup"><span data-stu-id="038b0-139">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="038b0-140">このような状況も EF は、構成を自動的に検出することと、次のいずれかの代わりに操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-140">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="038b0-141">」の説明に従って、構成ファイルで DbConfiguration 型を設定、*移動 DbConfiguration*前のセクション</span><span class="sxs-lookup"><span data-stu-id="038b0-141">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="038b0-142">アプリケーションの起動時に静的 DbConfiguration.SetConfiguration メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="038b0-142">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="038b0-143">DbConfiguration をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="038b0-143">Overriding DbConfiguration</span></span>  

<span data-ttu-id="038b0-144">状況によっては、DbConfiguration で設定された構成をオーバーライドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-144">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="038b0-145">これではなく、サード パーティ プロバイダーとプラグイン派生 DbConfiguration クラスを使用することはできませんが、アプリケーション開発者が通常行いますされません。</span><span class="sxs-lookup"><span data-stu-id="038b0-145">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="038b0-146">これは、EntityFramework がロック ダウンする前に、既存の構成を変更できるイベント ハンドラーを登録できます。</span><span class="sxs-lookup"><span data-stu-id="038b0-146">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="038b0-147">また、EF のサービス ロケーターによって返される任意のサービスを置き換えるためには、具体的には、sugar メソッドも提供します。</span><span class="sxs-lookup"><span data-stu-id="038b0-147">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="038b0-148">これは意図された使用する方法です。</span><span class="sxs-lookup"><span data-stu-id="038b0-148">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="038b0-149">(前に EF を使用すると)、アプリの起動時に、プラグインまたはプロバイダーがこのイベントのイベント ハンドラー メソッドを登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="038b0-149">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="038b0-150">(アプリケーションは、EF を使用する前にこれ行う必要がありますに注意してください)。</span><span class="sxs-lookup"><span data-stu-id="038b0-150">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="038b0-151">イベント ハンドラーは、ReplaceService への呼び出しを置き換える必要があるすべてのサービスです。</span><span class="sxs-lookup"><span data-stu-id="038b0-151">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="038b0-152">たとえば、repalce IDbConnectionFactory を DbProviderService は、次のようにハンドラーを登録しました。</span><span class="sxs-lookup"><span data-stu-id="038b0-152">For example, to repalce IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="038b0-153">MyProviderServices と MyConnectionFactory 上のコードでは、サービスの実装を表します。</span><span class="sxs-lookup"><span data-stu-id="038b0-153">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="038b0-154">同じ効果を取得する追加の依存関係のハンドラーを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="038b0-154">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="038b0-155">この方法で DbProviderFactory をラップすることもできますが、行うのためにのみ反映 EF と EF の外部で DbProviderFactory の使用しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="038b0-155">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="038b0-156">このため、引き続き DbProviderFactory をラップする前にある、したいがおそらくします。</span><span class="sxs-lookup"><span data-stu-id="038b0-156">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="038b0-157">必要がありますも留意するサービスを実行するアプリケーションの外部になど、パッケージ マネージャー コンソールからの移行を実行する場合。</span><span class="sxs-lookup"><span data-stu-id="038b0-157">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="038b0-158">実行すると、DbConfiguration を検索しようとする、コンソールから移行します。</span><span class="sxs-lookup"><span data-stu-id="038b0-158">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="038b0-159">ただし、where に依存かどうかを取得する、ラップされたサービスに登録されているイベント ハンドラー。</span><span class="sxs-lookup"><span data-stu-id="038b0-159">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="038b0-160">DbConfiguration の構築の一部として登録されている場合は、コードを実行する必要があり、サービスをラップする必要がありますを取得します。</span><span class="sxs-lookup"><span data-stu-id="038b0-160">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="038b0-161">通常このすることはできませんし、つまりツールは、ラップされたサービスを取得しません。</span><span class="sxs-lookup"><span data-stu-id="038b0-161">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
