---
title: シャドウ プロパティ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 4029539f3642f539a427f5901577d4df96c00f30
ms.sourcegitcommit: 119058fefd7f35952048f783ada68be9aa612256
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66749699"
---
# <a name="shadow-properties"></a><span data-ttu-id="5290e-102">シャドウ プロパティ</span><span class="sxs-lookup"><span data-stu-id="5290e-102">Shadow Properties</span></span>

<span data-ttu-id="5290e-103">シャドウ プロパティは、.NET のエンティティ クラスで定義されていないが、EF Core モデルでは、そのエンティティ型に対して定義されているプロパティ。</span><span class="sxs-lookup"><span data-stu-id="5290e-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="5290e-104">純粋な変更の追跡ツールでは、値とこれらのプロパティの状態が維持されます。</span><span class="sxs-lookup"><span data-stu-id="5290e-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="5290e-105">シャドウ プロパティは、マップされたエンティティ型で公開してはなりませんが、データベース内のデータがある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="5290e-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="5290e-106">外部キー プロパティ、2 つのエンティティ間のリレーションシップは、データベース内の外部キー値によって表されますが、リレーションシップがエンティティ型の間のナビゲーション プロパティを使用して、エンティティ型で管理されている、最もよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="5290e-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="5290e-107">シャドウ プロパティの値を入手して使用して変更することができます、 `ChangeTracker` API。</span><span class="sxs-lookup"><span data-stu-id="5290e-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="5290e-108">シャドウ プロパティを使用した LINQ クエリで参照できます、`EF.Property`静的メソッド。</span><span class="sxs-lookup"><span data-stu-id="5290e-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="5290e-109">規約</span><span class="sxs-lookup"><span data-stu-id="5290e-109">Conventions</span></span>

<span data-ttu-id="5290e-110">リレーションシップが検出されますが、依存エンティティ クラスの外部キー プロパティが見つからないときに、慣例シャドウ プロパティを作成できます。</span><span class="sxs-lookup"><span data-stu-id="5290e-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="5290e-111">この場合、影の外部キー プロパティが導入されます。</span><span class="sxs-lookup"><span data-stu-id="5290e-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="5290e-112">シャドウの外部キー プロパティの名前は`<navigation property name><principal key property name>`(プリンシパル エンティティを指す、依存エンティティのナビゲーションは、名前付けの使用)。</span><span class="sxs-lookup"><span data-stu-id="5290e-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="5290e-113">かどうかには、プリンシパルのキー プロパティの名前には、ナビゲーション プロパティの名前が含まれていますし、名前になります`<principal key property name>`します。</span><span class="sxs-lookup"><span data-stu-id="5290e-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="5290e-114">依存エンティティにナビゲーション プロパティがない場合、プリンシパルの種類名は、その代わりに使用されます。</span><span class="sxs-lookup"><span data-stu-id="5290e-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="5290e-115">たとえば、次のコード リストになります。、`BlogId`シャドウ プロパティがに導入される、`Post`エンティティ。</span><span class="sxs-lookup"><span data-stu-id="5290e-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="5290e-116">データの注釈</span><span class="sxs-lookup"><span data-stu-id="5290e-116">Data Annotations</span></span>

<span data-ttu-id="5290e-117">シャドウ プロパティを作成して、データ注釈を使用したことはできません。</span><span class="sxs-lookup"><span data-stu-id="5290e-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="5290e-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5290e-118">Fluent API</span></span>

<span data-ttu-id="5290e-119">Fluent API を使用して、シャドウ プロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="5290e-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="5290e-120">文字列のオーバー ロードを呼び出した後`Property`他のプロパティの構成の呼び出しを連結することができます。</span><span class="sxs-lookup"><span data-stu-id="5290e-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="5290e-121">名前が指定されている場合、`Property`メソッド (シャドウ プロパティまたはエンティティ クラスで定義されているいずれか)、既存のプロパティの名前に一致し、コードでは新しいシャドウ プロパティを導入するのではなく、その既存のプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="5290e-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

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
