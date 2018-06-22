---
title: シャドウのプロパティ - EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
ms.technology: entity-framework-core
uid: core/modeling/shadow-properties
ms.openlocfilehash: 8c7f70789ddc6ebd24f9bfae931069834db593c2
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053552"
---
# <a name="shadow-properties"></a><span data-ttu-id="65052-102">プロパティをシャドウ</span><span class="sxs-lookup"><span data-stu-id="65052-102">Shadow Properties</span></span>

<span data-ttu-id="65052-103">シャドウのプロパティは、.NET エンティティ クラスで定義されていないが、EF の主要なモデルでは、そのエンティティ型に対して定義されているプロパティです。</span><span class="sxs-lookup"><span data-stu-id="65052-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="65052-104">変更トラッカーの純粋値とこれらのプロパティの状態が維持されます。</span><span class="sxs-lookup"><span data-stu-id="65052-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="65052-105">シャドウのプロパティは、マップされているエンティティ型で公開する必要がないデータベース内のデータがある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="65052-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="65052-106">ほとんどの場合、データベースの外部キーの値で表される 2 つのエンティティ間のリレーションシップは、リレーションシップは、エンティティ型の間のナビゲーション プロパティを使用してエンティティの種類で管理されていますが、外部キー プロパティの使用されます。</span><span class="sxs-lookup"><span data-stu-id="65052-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="65052-107">シャドウ プロパティの値を入手して使用して変更することができます、 `ChangeTracker` API です。</span><span class="sxs-lookup"><span data-stu-id="65052-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
   context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="65052-108">プロパティをシャドウを使用した LINQ クエリで参照できる、`EF.Property`静的メソッドです。</span><span class="sxs-lookup"><span data-stu-id="65052-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="65052-109">規則</span><span class="sxs-lookup"><span data-stu-id="65052-109">Conventions</span></span>

<span data-ttu-id="65052-110">リレーションシップが検出されますが、依存エンティティ クラスの外部キーのプロパティが見つからないときに、慣例によりシャドウのプロパティを作成できます。</span><span class="sxs-lookup"><span data-stu-id="65052-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="65052-111">この場合、シャドウ外部キーのプロパティが導入されます。</span><span class="sxs-lookup"><span data-stu-id="65052-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="65052-112">シャドウの外部キー プロパティの名前は`<navigation property name><principal key property name>`(プリンシパル エンティティを指す、依存エンティティのナビゲーションは、名前付けの使用)。</span><span class="sxs-lookup"><span data-stu-id="65052-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="65052-113">プリンシパル キー プロパティ名がナビゲーション プロパティの名前を含むかどうかは、名前は単なる`<principal key property name>`です。</span><span class="sxs-lookup"><span data-stu-id="65052-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="65052-114">依存エンティティにナビゲーション プロパティがない場合、プリンシパルの種類名は、代わりに使用されます。</span><span class="sxs-lookup"><span data-stu-id="65052-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="65052-115">たとえば、次のコード リストが結果の`BlogId`シャドウ プロパティに導入する際に、`Post`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="65052-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="65052-116">データの注釈</span><span class="sxs-lookup"><span data-stu-id="65052-116">Data Annotations</span></span>

<span data-ttu-id="65052-117">データ注釈を使用したプロパティをシャドウを作成できません。</span><span class="sxs-lookup"><span data-stu-id="65052-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="65052-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="65052-118">Fluent API</span></span>

<span data-ttu-id="65052-119">Fluent API を使用して、シャドウ プロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="65052-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="65052-120">文字列のオーバー ロードを呼び出した後`Property`他のプロパティは構成の呼び出しのいずれかにチェーンすることができます。</span><span class="sxs-lookup"><span data-stu-id="65052-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="65052-121">名前を指定する場合、`Property`メソッド (シャドウ プロパティまたはエンティティ クラスで定義されているいずれか)、既存のプロパティの名前に一致し、コードで新しいシャドウ プロパティを導入するのではなく、その既存のプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="65052-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
