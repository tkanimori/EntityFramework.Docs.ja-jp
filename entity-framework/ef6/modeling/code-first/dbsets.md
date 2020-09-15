---
title: 定義 (DbSets を)-EF6
description: Entity Framework 6 での DbSets の定義
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 49ffe3ddb8cd4f7c414d7d45d108405b88a0115e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073952"
---
# <a name="defining-dbsets"></a><span data-ttu-id="cb563-103">定義 (DbSets を)</span><span class="sxs-lookup"><span data-stu-id="cb563-103">Defining DbSets</span></span>
<span data-ttu-id="cb563-104">Code First ワークフローを使用して開発する場合は、データベースとのセッションを表す派生 DbContext を定義し、モデルの型ごとに Dbcontext を公開します。</span><span class="sxs-lookup"><span data-stu-id="cb563-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="cb563-105">このトピックでは、DbSet プロパティを定義するさまざまな方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cb563-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="cb563-106">DbContext と Dbcontext プロパティ</span><span class="sxs-lookup"><span data-stu-id="cb563-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="cb563-107">Code First 例に示す一般的なケースは、モデルのエンティティ型に対して、パブリックな自動 Dbcontext プロパティを持つ DbContext を使用することです。</span><span class="sxs-lookup"><span data-stu-id="cb563-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="cb563-108">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cb563-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="cb563-109">Code First モードで使用すると、ブログや投稿をエンティティ型として構成できるだけでなく、その他の型を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cb563-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="cb563-110">さらに、DbContext は、これらの各プロパティの setter を自動的に呼び出して、適切な Dbcontext のインスタンスを設定します。</span><span class="sxs-lookup"><span data-stu-id="cb563-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="cb563-111">IDbSet プロパティを持つ DbContext</span><span class="sxs-lookup"><span data-stu-id="cb563-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="cb563-112">モックの作成時やフェイクの作成時など、インターフェイスを使用して set プロパティを宣言すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="cb563-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="cb563-113">このような場合は、DbSet の代わりに IDbSet インターフェイスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb563-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="cb563-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cb563-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="cb563-115">このコンテキストは、その設定プロパティに DbSet クラスを使用するコンテキストとまったく同じ方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="cb563-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="cb563-116">読み取り専用の設定プロパティを持つ DbContext</span><span class="sxs-lookup"><span data-stu-id="cb563-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="cb563-117">DbSet または IDbSet プロパティのパブリック setter を公開したくない場合は、代わりに読み取り専用プロパティを作成し、自分で set インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="cb563-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="cb563-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cb563-118">For example:</span></span>  

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

<span data-ttu-id="cb563-119">DbContext は、Set メソッドから返された Dbcontext のインスタンスをキャッシュして、これらの各プロパティが呼び出されるたびに同じインスタンスを返すようにします。</span><span class="sxs-lookup"><span data-stu-id="cb563-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="cb563-120">Code First のエンティティ型の検出は、パブリックな getter と setter を持つプロパティの場合と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="cb563-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
