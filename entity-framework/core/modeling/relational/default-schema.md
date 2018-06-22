---
title: 既定のスキーマの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
ms.technology: entity-framework-core
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 26106deb2d4e35ecf33e97790a83f9af77991aed
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052752"
---
# <a name="default-schema"></a>[既定のスキーマ]

> [!NOTE]  
> このセクションの構成は、一般にリレーショナル データベースに適用されます。 ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。

既定のスキーマは、そのオブジェクトのスキーマが明示的に構成されていない場合にオブジェクトを作成するデータベースのスキーマです。

## <a name="conventions"></a>規則

慣例により、データベース プロバイダーは、最も適切な既定のスキーマを選択します。 たとえば、Microsoft SQL Server が使用されます、`dbo`スキーマと SQLite は使用しませんスキーマ (からのスキーマは、SQLite ではサポートされていません)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して既定のスキーマを設定することはできません。

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
