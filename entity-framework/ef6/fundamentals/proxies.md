---
title: プロキシの使用-EF6
description: Entity Framework 6 でのプロキシの使用
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: e626a7dff67497a2fbb3dcd169704814ea4cf2cf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070498"
---
# <a name="working-with-proxies"></a><span data-ttu-id="48879-103">プロキシの使用</span><span class="sxs-lookup"><span data-stu-id="48879-103">Working with proxies</span></span>
<span data-ttu-id="48879-104">POCO エンティティ型のインスタンスを作成する場合、Entity Framework は、多くの場合、エンティティのプロキシとして機能する動的に生成された派生型のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="48879-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="48879-105">このプロキシは、プロパティにアクセスしたときにアクションを自動的に実行するフックを挿入するために、エンティティの一部の仮想プロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="48879-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="48879-106">たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="48879-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="48879-107">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="48879-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="48879-108">プロキシ作成の無効化</span><span class="sxs-lookup"><span data-stu-id="48879-108">Disabling proxy creation</span></span>  

<span data-ttu-id="48879-109">Entity Framework がプロキシインスタンスを作成しないようにすると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="48879-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="48879-110">たとえば、プロキシインスタンスをシリアル化するよりも、プロキシ以外のインスタンスをシリアル化する方がはるかに簡単です。</span><span class="sxs-lookup"><span data-stu-id="48879-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="48879-111">プロキシの作成は、Proxyの有効化フラグをオフにすることによって無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="48879-111">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="48879-112">これを実行できる1つの方法は、コンテキストのコンストラクターです。</span><span class="sxs-lookup"><span data-stu-id="48879-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="48879-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48879-113">For example:</span></span>  

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

<span data-ttu-id="48879-114">EF では、プロキシが何も実行しない型に対してプロキシを作成しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="48879-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="48879-115">これは、シールされた型または仮想プロパティを持たない型を使用してプロキシを回避することもできます。</span><span class="sxs-lookup"><span data-stu-id="48879-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="48879-116">プロキシのインスタンスを明示的に作成する</span><span class="sxs-lookup"><span data-stu-id="48879-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="48879-117">New 演算子を使用してエンティティのインスタンスを作成した場合、プロキシインスタンスは作成されません。</span><span class="sxs-lookup"><span data-stu-id="48879-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="48879-118">これは問題になることはありませんが、プロキシインスタンスを作成する必要がある場合 (たとえば、遅延読み込みやプロキシ変更の追跡が機能するようにする場合)、DbSet の Create メソッドを使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="48879-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="48879-119">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48879-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="48879-120">派生エンティティ型のインスタンスを作成する場合は、ジェネリックバージョンの Create を使用できます。</span><span class="sxs-lookup"><span data-stu-id="48879-120">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="48879-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48879-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="48879-122">Create メソッドでは、作成されたエンティティがコンテキストに追加またはアタッチされないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="48879-122">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="48879-123">Create メソッドは、エンティティ型自体のインスタンスを作成するだけであることに注意してください。エンティティのプロキシ型を作成すると、何も実行されないため、値がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="48879-123">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="48879-124">たとえば、エンティティ型が sealed であるか、またはに仮想プロパティがない場合、Create はエンティティ型のインスタンスを作成するだけです。</span><span class="sxs-lookup"><span data-stu-id="48879-124">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="48879-125">プロキシ型からの実際のエンティティ型の取得</span><span class="sxs-lookup"><span data-stu-id="48879-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="48879-126">プロキシ型には、次のような名前が付いています。</span><span class="sxs-lookup"><span data-stu-id="48879-126">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="48879-127">System.object.... Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="48879-127">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="48879-128">このプロキシ型のエンティティ型は、ObjectContext の GetObjectType メソッドを使用して見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="48879-128">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="48879-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48879-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="48879-130">GetObjectType に渡される型が、プロキシ型ではないエンティティ型のインスタンスである場合、エンティティの型は引き続き返されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="48879-130">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="48879-131">つまり、このメソッドを使用して、型がプロキシ型であるかどうかを確認する他のチェックを行わずに、実際のエンティティ型を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="48879-131">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
