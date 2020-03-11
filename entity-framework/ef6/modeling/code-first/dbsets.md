---
title: 定義 (DbSets を)-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
ms.openlocfilehash: 045b22d2b9d26804948689dd7c9dd694baadda7e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415779"
---
# <a name="defining-dbsets"></a><span data-ttu-id="c8f01-102">定義 (DbSets を)</span><span class="sxs-lookup"><span data-stu-id="c8f01-102">Defining DbSets</span></span>
<span data-ttu-id="c8f01-103">Code First ワークフローを使用して開発する場合は、データベースとのセッションを表す派生 DbContext を定義し、モデルの型ごとに Dbcontext を公開します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-103">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="c8f01-104">このトピックでは、DbSet プロパティを定義するさまざまな方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-104">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="c8f01-105">DbContext と Dbcontext プロパティ</span><span class="sxs-lookup"><span data-stu-id="c8f01-105">DbContext with DbSet properties</span></span>  

<span data-ttu-id="c8f01-106">Code First 例に示す一般的なケースは、モデルのエンティティ型に対して、パブリックな自動 Dbcontext プロパティを持つ DbContext を使用することです。</span><span class="sxs-lookup"><span data-stu-id="c8f01-106">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="c8f01-107">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-107">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="c8f01-108">Code First モードで使用すると、ブログや投稿をエンティティ型として構成できるだけでなく、その他の型を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c8f01-108">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="c8f01-109">さらに、DbContext は、これらの各プロパティの setter を自動的に呼び出して、適切な Dbcontext のインスタンスを設定します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-109">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="c8f01-110">IDbSet プロパティを持つ DbContext</span><span class="sxs-lookup"><span data-stu-id="c8f01-110">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="c8f01-111">モックの作成時やフェイクの作成時など、インターフェイスを使用して set プロパティを宣言すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c8f01-111">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="c8f01-112">このような場合は、DbSet の代わりに IDbSet インターフェイスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8f01-112">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="c8f01-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="c8f01-114">このコンテキストは、その設定プロパティに DbSet クラスを使用するコンテキストとまったく同じ方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-114">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="c8f01-115">読み取り専用の設定プロパティを持つ DbContext</span><span class="sxs-lookup"><span data-stu-id="c8f01-115">DbContext with read-only set properties</span></span>  

<span data-ttu-id="c8f01-116">DbSet または IDbSet プロパティのパブリック setter を公開したくない場合は、代わりに読み取り専用プロパティを作成し、自分で set インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c8f01-116">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="c8f01-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-117">For example:</span></span>  

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

<span data-ttu-id="c8f01-118">DbContext は、Set メソッドから返された Dbcontext のインスタンスをキャッシュして、これらの各プロパティが呼び出されるたびに同じインスタンスを返すようにします。</span><span class="sxs-lookup"><span data-stu-id="c8f01-118">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="c8f01-119">Code First のエンティティ型の検出は、パブリックな getter と setter を持つプロパティの場合と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="c8f01-119">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
