---
title: キー (プライマリ) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 6272e323b83ccab2ed060a2ebbde1d1e8e353d66
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319167"
---
# <a name="keys-primary"></a>キー (プライマリ)

キーは、各エンティティ インスタンスのプライマリの一意の識別子として機能します。 リレーショナル データベースを使用する場合はこれはマップの概念、*主キー*します。 主キーでない一意の識別子を構成することもできます (を参照してください[代替キー](alternate-keys.md)詳細については)。 

次のいずれかのセットアップ/主キーの作成に使用できます。

## <a name="conventions"></a>規約

プロパティの名前付け規則、により`Id`または`<type name>Id`はエンティティのキーとして構成されます。

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

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。

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

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、エンティティのキーにする 1 つのプロパティを構成することができます。

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

キー (複合キーと呼ばれる) エンティティの複数のプロパティを構成するのに Fluent API を使用することもできます。 Fluent API を使用して複合キーを構成することができますのみ - 規則は複合キーをセットアップしないと、いずれかを構成するデータの注釈は使用できません。

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
