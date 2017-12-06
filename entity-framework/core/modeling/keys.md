---
title: "キー (プライマリ) - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
ms.technology: entity-framework-core
uid: core/modeling/keys
ms.openlocfilehash: f3bf3c7f2a28e065b350fe000a5164406cd5ca08
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="keys-primary"></a><span data-ttu-id="74434-102">キー (プライマリ)</span><span class="sxs-lookup"><span data-stu-id="74434-102">Keys (primary)</span></span>

<span data-ttu-id="74434-103">キーは、各エンティティ インスタンスの主な一意の識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="74434-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="74434-104">リレーショナル データベースを使用する場合はこれはマップの概念、*主キー*です。</span><span class="sxs-lookup"><span data-stu-id="74434-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="74434-105">主キーではない一意の識別子を構成することもできます (を参照してください[代替キー](alternate-keys.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="74434-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

## <a name="conventions"></a><span data-ttu-id="74434-106">規則</span><span class="sxs-lookup"><span data-stu-id="74434-106">Conventions</span></span>

<span data-ttu-id="74434-107">プロパティの名前付け規則、により`Id`または`<type name>Id`エンティティのキーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="74434-107">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="74434-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="74434-108">Data Annotations</span></span>

<span data-ttu-id="74434-109">データ注釈を使用して、エンティティのキーとして使用する 1 つのプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="74434-109">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

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

## <a name="fluent-api"></a><span data-ttu-id="74434-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="74434-110">Fluent API</span></span>

<span data-ttu-id="74434-111">Fluent API を使用して、エンティティのキーとして使用する 1 つのプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="74434-111">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

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

<span data-ttu-id="74434-112">(複合キーと呼ばれます)、エンティティのキーとして使用するのに複数のプロパティを構成するのに Fluent API を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="74434-112">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="74434-113">Fluent API を使用して複合キーを構成することができますのみ - 規則が複合キーをセットアップしないといずれかを構成データ注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="74434-113">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

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
