---
title: プロキシの使用-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415989"
---
# <a name="working-with-proxies"></a><span data-ttu-id="53089-102">プロキシの使用</span><span class="sxs-lookup"><span data-stu-id="53089-102">Working with proxies</span></span>
<span data-ttu-id="53089-103">POCO エンティティ型のインスタンスを作成する場合、Entity Framework は、多くの場合、エンティティのプロキシとして機能する動的に生成された派生型のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="53089-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="53089-104">このプロキシは、プロパティにアクセスしたときにアクションを自動的に実行するフックを挿入するために、エンティティの一部の仮想プロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="53089-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="53089-105">たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="53089-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="53089-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="53089-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="53089-107">プロキシ作成の無効化</span><span class="sxs-lookup"><span data-stu-id="53089-107">Disabling proxy creation</span></span>  

<span data-ttu-id="53089-108">Entity Framework がプロキシインスタンスを作成しないようにすると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="53089-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="53089-109">たとえば、プロキシインスタンスをシリアル化するよりも、プロキシ以外のインスタンスをシリアル化する方がはるかに簡単です。</span><span class="sxs-lookup"><span data-stu-id="53089-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="53089-110">プロキシの作成は、Proxyの有効化フラグをオフにすることによって無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="53089-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="53089-111">これを実行できる1つの方法は、コンテキストのコンストラクターです。</span><span class="sxs-lookup"><span data-stu-id="53089-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="53089-112">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53089-112">For example:</span></span>  

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

<span data-ttu-id="53089-113">EF では、プロキシが何も実行しない型に対してプロキシを作成しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53089-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="53089-114">これは、シールされた型または仮想プロパティを持たない型を使用してプロキシを回避することもできます。</span><span class="sxs-lookup"><span data-stu-id="53089-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="53089-115">プロキシのインスタンスを明示的に作成する</span><span class="sxs-lookup"><span data-stu-id="53089-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="53089-116">New 演算子を使用してエンティティのインスタンスを作成した場合、プロキシインスタンスは作成されません。</span><span class="sxs-lookup"><span data-stu-id="53089-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="53089-117">これは問題になることはありませんが、プロキシインスタンスを作成する必要がある場合 (たとえば、遅延読み込みやプロキシ変更の追跡が機能するようにする場合)、DbSet の Create メソッドを使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="53089-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="53089-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53089-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="53089-119">派生エンティティ型のインスタンスを作成する場合は、ジェネリックバージョンの Create を使用できます。</span><span class="sxs-lookup"><span data-stu-id="53089-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="53089-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53089-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="53089-121">Create メソッドでは、作成されたエンティティがコンテキストに追加またはアタッチされないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53089-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="53089-122">Create メソッドは、エンティティ型自体のインスタンスを作成するだけであることに注意してください。エンティティのプロキシ型を作成すると、何も実行されないため、値がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53089-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="53089-123">たとえば、エンティティ型が sealed であるか、またはに仮想プロパティがない場合、Create はエンティティ型のインスタンスを作成するだけです。</span><span class="sxs-lookup"><span data-stu-id="53089-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="53089-124">プロキシ型からの実際のエンティティ型の取得</span><span class="sxs-lookup"><span data-stu-id="53089-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="53089-125">プロキシ型には、次のような名前が付いています。</span><span class="sxs-lookup"><span data-stu-id="53089-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="53089-126">System.object.... Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="53089-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="53089-127">このプロキシ型のエンティティ型は、ObjectContext の GetObjectType メソッドを使用して見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="53089-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="53089-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53089-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="53089-129">GetObjectType に渡される型が、プロキシ型ではないエンティティ型のインスタンスである場合、エンティティの型は引き続き返されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53089-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="53089-130">つまり、このメソッドを使用して、型がプロキシ型であるかどうかを確認する他のチェックを行わずに、実際のエンティティ型を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="53089-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
