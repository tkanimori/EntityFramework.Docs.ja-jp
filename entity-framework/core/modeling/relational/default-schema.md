---
title: EF Core - 既定のスキーマ
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 800551bbadd0a9e8b5eb7070a8ccf6ed2407e3d2
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995367"
---
# <a name="default-schema"></a>既定のスキーマ

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

既定のスキーマは、そのオブジェクトのスキーマが明示的に構成されていない場合にオブジェクトを作成するデータベース スキーマです。

## <a name="conventions"></a>規約

慣例により、データベース プロバイダーは、最も適切な既定のスキーマを選択します。 たとえば、Microsoft SQL Server が使用して、`dbo`スキーマと SQLite されますので使用しませんスキーマ (スキーマは、SQLite ではサポートされていない)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して既定のスキーマを設定することができません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用すると、既定のスキーマを指定します。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/DefaultSchema.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("blogging");
    }
}
```
