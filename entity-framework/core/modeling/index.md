---
title: "モデルの作成 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
ms.technology: entity-framework-core
uid: core/modeling/index
ms.openlocfilehash: 1ad0f6891fbc8ba2e4d102cc9997f053a9dddb66
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="creating-a-model"></a><span data-ttu-id="03c56-102">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="03c56-102">Creating a Model</span></span>

<span data-ttu-id="03c56-103">Entity Framework では、一連の規則を利用し、エンティティ クラスの形状に基づいてモデルがビルドされます。</span><span class="sxs-lookup"><span data-stu-id="03c56-103">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="03c56-104">規則にあるものを捕捉する/オーバーライドする目的で、追加構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="03c56-104">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="03c56-105">この記事では、あらゆるデータ ストアをターゲットにするモデルに適用できる構成、あらゆるリレーショナル データベースをターゲットにするときに適用できる構成について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="03c56-105">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="03c56-106">プロバイダーも、特定のデータ ストアに固有の構成を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="03c56-106">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="03c56-107">プロバイダー固有の構成については、「[データベース プロバイダー](../providers/index.md)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03c56-107">For documentation on provider specific configuration see the [Database Providers](../providers/index.md) section.</span></span>

> [!TIP]  
> <span data-ttu-id="03c56-108">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="03c56-108">You can view this article’s [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="methods-of-configuration"></a><span data-ttu-id="03c56-109">構成の方法</span><span class="sxs-lookup"><span data-stu-id="03c56-109">Methods of configuration</span></span>

### <a name="fluent-api"></a><span data-ttu-id="03c56-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="03c56-110">Fluent API</span></span>

<span data-ttu-id="03c56-111">派生コンテキストで `OnModelCreating` メソッドをオーバーライドし、`ModelBuilder API` を使用してモデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="03c56-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="03c56-112">これは最も強力な構成方法であり、エンティティ クラスを変更しなくても構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="03c56-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="03c56-113">Fluent API 構成には一番上の優先度が与えられ、規則やデータ注釈をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03c56-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?range=5-15&highlight=5-10)] -->

``` csharp
    class MyContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>()
                .Property(b => b.Url)
                .IsRequired();
        }
    }
```

### <a name="data-annotations"></a><span data-ttu-id="03c56-114">データの注釈</span><span class="sxs-lookup"><span data-stu-id="03c56-114">Data Annotations</span></span>

<span data-ttu-id="03c56-115">クラスやプロパティに属性 (データ注釈と呼ばれています) を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="03c56-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="03c56-116">データ注釈は規則をオーバーライドしますが、Fluent API 構成に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="03c56-116">Data annotations will override conventions, but will be overwritten by Fluent API configuration.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?range=11-16&highlight=4)] -->
``` csharp
    public class Blog
    {
        public int BlogId { get; set; }
        [Required]
        public string Url { get; set; }
    }
```
