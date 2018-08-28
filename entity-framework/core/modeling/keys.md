---
title: キー (プライマリ) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 9e6946100ebabc6ba57cb792b3672219098b1e21
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994022"
---
# <a name="keys-primary"></a><span data-ttu-id="b493e-102">キー (プライマリ)</span><span class="sxs-lookup"><span data-stu-id="b493e-102">Keys (primary)</span></span>

<span data-ttu-id="b493e-103">キーは、各エンティティ インスタンスのプライマリの一意の識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="b493e-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="b493e-104">リレーショナル データベースを使用する場合はこれはマップの概念、*主キー*します。</span><span class="sxs-lookup"><span data-stu-id="b493e-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="b493e-105">主キーでない一意の識別子を構成することもできます (を参照してください[代替キー](alternate-keys.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="b493e-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

## <a name="conventions"></a><span data-ttu-id="b493e-106">規約</span><span class="sxs-lookup"><span data-stu-id="b493e-106">Conventions</span></span>

<span data-ttu-id="b493e-107">プロパティの名前付け規則、により`Id`または`<type name>Id`はエンティティのキーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="b493e-107">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="b493e-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b493e-108">Data Annotations</span></span>

<span data-ttu-id="b493e-109">データ注釈を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="b493e-109">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=3,4)] -->
``` csharp
class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="b493e-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b493e-110">Fluent API</span></span>

<span data-ttu-id="b493e-111">Fluent API を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="b493e-111">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => c.LicensePlate);
    }
}

class Car
{
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<span data-ttu-id="b493e-112">キー (複合キーと呼ばれる) エンティティの複数のプロパティを構成するのに Fluent API を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="b493e-112">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="b493e-113">Fluent API を使用して複合キーを構成することができますのみ - 規則は複合キーをセットアップしないと、いずれかを構成するデータの注釈は使用できません。</span><span class="sxs-lookup"><span data-stu-id="b493e-113">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```
