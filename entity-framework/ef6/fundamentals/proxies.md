---
title: プロキシ - EF6 の操作
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489818"
---
# <a name="working-with-proxies"></a><span data-ttu-id="75e3d-102">プロキシの操作</span><span class="sxs-lookup"><span data-stu-id="75e3d-102">Working with proxies</span></span>
<span data-ttu-id="75e3d-103">POCO エンティティ型のインスタンスを作成するときに Entity Framework は多くの場合、動的に生成されたエンティティのプロキシとして機能する派生型のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="75e3d-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="75e3d-104">このプロキシは、プロパティにアクセスするときにアクションを自動的に実行するためのフックを挿入するエンティティの一部の仮想プロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="75e3d-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="75e3d-105">たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートに使用されます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="75e3d-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="75e3d-107">プロキシの作成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="75e3d-107">Disabling proxy creation</span></span>  

<span data-ttu-id="75e3d-108">Entity Framework がプロキシのインスタンスを作成するを防ぐために便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="75e3d-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="75e3d-109">たとえば、プロキシではないインスタンスをシリアル化はプロキシ インスタンスをシリアル化するよりもはるかに簡単にします。</span><span class="sxs-lookup"><span data-stu-id="75e3d-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="75e3d-110">ProxyCreationEnabled フラグをオフにすると、プロキシの作成をオフにすることができます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="75e3d-111">これを行うことが 1 つの場所のコンテキストのコンス トラクター内です。</span><span class="sxs-lookup"><span data-stu-id="75e3d-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="75e3d-112">例えば:</span><span class="sxs-lookup"><span data-stu-id="75e3d-112">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="75e3d-113">EF によって型プロキシは作成されないことに注意してください。 何を行うプロキシを使用する必要がある場合。</span><span class="sxs-lookup"><span data-stu-id="75e3d-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="75e3d-114">つまり、型はシールされてや仮想プロパティを持っていないをすることでプロキシも回避できます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="75e3d-115">プロキシのインスタンスを明示的に作成します。</span><span class="sxs-lookup"><span data-stu-id="75e3d-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="75e3d-116">新しい演算子を使用してエンティティのインスタンスを作成する場合、プロキシ インスタンスは作成されません。</span><span class="sxs-lookup"><span data-stu-id="75e3d-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="75e3d-117">問題ないことが考えられますが、(たとえば、遅延読み込みまたはプロキシ変更の追跡が動作するよう) プロキシ インスタンスを作成する必要がある場合、行うことができます、DbSet の Create メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="75e3d-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="75e3d-118">例えば:</span><span class="sxs-lookup"><span data-stu-id="75e3d-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="75e3d-119">ジェネリック バージョンを作成するは、派生エンティティ型のインスタンスを作成する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="75e3d-120">例えば:</span><span class="sxs-lookup"><span data-stu-id="75e3d-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="75e3d-121">Create メソッドを追加または作成されたエンティティをコンテキストにアタッチはいないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="75e3d-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="75e3d-122">エントリのエンティティのプロキシ型を作成する場合、Create メソッドは、エンティティ型自体のインスタンスが作成だけはありません値それとは何も実行するため。</span><span class="sxs-lookup"><span data-stu-id="75e3d-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="75e3d-123">たとえば、エンティティ型がシールされている場合や作成は、仮想プロパティを持たない場合は、エンティティ型のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="75e3d-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="75e3d-124">プロキシの種類からの実際のエンティティ型の取得</span><span class="sxs-lookup"><span data-stu-id="75e3d-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="75e3d-125">プロキシ型は、このようなものを検索する名前を持ちます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="75e3d-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="75e3d-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="75e3d-127">このプロキシ型が ObjectContext から GetObjectType メソッドを使用してエンティティ型が表示されます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="75e3d-128">例えば:</span><span class="sxs-lookup"><span data-stu-id="75e3d-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="75e3d-129">GetObjectType に渡される型の場合は、プロキシ型のエンティティ型ではないエンティティ型のインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="75e3d-130">つまり、実際のエンティティ型を取得するか、型がプロキシ型の場合に表示するその他のチェックなしにこのメソッドを常に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="75e3d-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
