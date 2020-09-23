---
title: 関連データの遅延読み込み - EF Core
description: Entity Framework Core による関連データの遅延読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: c42cde469e2be38d53a46cb6c5c252a088978e5c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078877"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="a7bff-103">関連データの遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="a7bff-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="a7bff-104">プロキシを使用した遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="a7bff-104">Lazy loading with proxies</span></span>

<span data-ttu-id="a7bff-105">遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="a7bff-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="a7bff-106">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a7bff-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="a7bff-107">または、AddDbContext を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="a7bff-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="a7bff-108">EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスのナビゲーション プロパティである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7bff-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="a7bff-109">たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。</span><span class="sxs-lookup"><span data-stu-id="a7bff-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="a7bff-110">プロキシを使用しない遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="a7bff-110">Lazy loading without proxies</span></span>

<span data-ttu-id="a7bff-111">遅延読み込みプロキシは、[エンティティ型コンストラクター](xref:core/modeling/constructors)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。</span><span class="sxs-lookup"><span data-stu-id="a7bff-111">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="a7bff-112">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a7bff-112">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="a7bff-113">このメソッドでは、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="a7bff-113">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="a7bff-114">ただし、これには、[Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) パッケージで定義される `ILazyLoader` サービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="a7bff-114">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="a7bff-115">このパッケージに含まれる型のセットは、それに依存してもほとんど影響がないように、最低限のセットになっています。</span><span class="sxs-lookup"><span data-stu-id="a7bff-115">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="a7bff-116">しかし、エンティティ型で EF Core パッケージへの依存を完全に回避するには、デリゲートとして `ILazyLoader.Load` メソッドを挿入することが可能です。</span><span class="sxs-lookup"><span data-stu-id="a7bff-116">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="a7bff-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a7bff-117">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="a7bff-118">上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="a7bff-118">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a7bff-119">遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7bff-119">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="a7bff-120">これとは別の名前を使用する構成が今後のリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="a7bff-120">Configuration to use a different name than this is planned for a future release.</span></span>
