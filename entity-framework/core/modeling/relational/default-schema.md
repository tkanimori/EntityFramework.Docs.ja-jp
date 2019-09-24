---
title: 既定のスキーマ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: ae903ed7200859430aecc55073651236759bc6ce
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197129"
---
# <a name="default-schema"></a>既定のスキーマ

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

既定のスキーマは、そのオブジェクトに対してスキーマが明示的に構成されていない場合にオブジェクトが作成されるデータベーススキーマです。

## <a name="conventions"></a>規約

慣例により、データベースプロバイダーは最も適切な既定のスキーマを選択します。 たとえば、Microsoft SQL Server では`dbo`スキーマが使用され、sqlite ではスキーマが使用されません (スキーマは sqlite ではサポートされていないため)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して既定のスキーマを設定することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、既定のスキーマを指定できます。

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultSchema.cs?highlight=7)] -->
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
