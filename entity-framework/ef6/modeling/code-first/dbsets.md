---
title: EF6 の DbSets を定義します。
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
caps.latest.revision: 3
ms.openlocfilehash: 8a495c6ce74d9a346a84b0e10fb28395f4dce07b
ms.sourcegitcommit: 00cb52625b57c1ea339ded1454179fe89b6bcfea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2018
ms.locfileid: "39123269"
---
# <a name="defining-dbsets"></a><span data-ttu-id="50786-102">DbSets を定義します。</span><span class="sxs-lookup"><span data-stu-id="50786-102">Defining DbSets</span></span>
<span data-ttu-id="50786-103">Code First ワークフローを開発する際に、データベースとのセッションを表し、モデル内の各型の DbSet を公開する DbContext 派生を定義します。</span><span class="sxs-lookup"><span data-stu-id="50786-103">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="50786-104">このトピックでは、DbSet プロパティを定義するさまざまな方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="50786-104">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="50786-105">DbContext の DbSet プロパティ</span><span class="sxs-lookup"><span data-stu-id="50786-105">DbContext with DbSet properties</span></span>  

<span data-ttu-id="50786-106">最初のコード例に示すように、一般的なケースは、モデルのエンティティ型のパブリックの自動 DbSet プロパティを DbContext です。</span><span class="sxs-lookup"><span data-stu-id="50786-106">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="50786-107">例えば:</span><span class="sxs-lookup"><span data-stu-id="50786-107">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="50786-108">Code First モードで使用する場合、エンティティ型だけから到達可能なその他の種類の構成として、ブログや投稿に設定されます。</span><span class="sxs-lookup"><span data-stu-id="50786-108">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="50786-109">さらに DbContext では、適切な DbSet のインスタンスを設定するには、各プロパティの setter は呼び出しが自動的に。</span><span class="sxs-lookup"><span data-stu-id="50786-109">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="50786-110">DbContext IDbSet プロパティを持つ</span><span class="sxs-lookup"><span data-stu-id="50786-110">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="50786-111">ときに作成するモックまたは fakes、インターフェイスを使用して、セットのプロパティの宣言をより便利ななどの場合があります。</span><span class="sxs-lookup"><span data-stu-id="50786-111">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="50786-112">このような場合、IDbSet DbSet の代わりにインターフェイスを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="50786-112">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="50786-113">例えば:</span><span class="sxs-lookup"><span data-stu-id="50786-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="50786-114">このコンテキストは、そのプロパティの設定の DbSet クラスを使用するコンテキストとまったく同じ方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="50786-114">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="50786-115">プロパティを設定する読み取り専用の DbContext</span><span class="sxs-lookup"><span data-stu-id="50786-115">DbContext with read-only set properties</span></span>  

<span data-ttu-id="50786-116">DbSet または IDbSet のプロパティのパブリック setter を公開したくない場合代わりに読み取り専用プロパティを作成し、自分で、セットのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="50786-116">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="50786-117">例えば:</span><span class="sxs-lookup"><span data-stu-id="50786-117">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="50786-118">DbContext はそれが呼び出されるたびに、同じインスタンスがこれらの各プロパティが返されるように、Set メソッドから返された DbSet のインスタンスをキャッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="50786-118">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="50786-119">ここで、同じ方法での Code First のしくみのエンティティ型の検出ほどには、パブリック getter および setter プロパティは。</span><span class="sxs-lookup"><span data-stu-id="50786-119">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
