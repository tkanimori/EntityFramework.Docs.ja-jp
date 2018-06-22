---
title: 最大長の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
ms.technology: entity-framework-core
uid: core/modeling/max-length
ms.openlocfilehash: 7325c0c3328477473392bf9e7c82f1696bb4f424
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052672"
---
# <a name="maximum-length"></a>最大長

最大の長さに設定する特定のプロパティを使用する適切なデータ型のデータ ストアへのヒントを提供します。 最大長にのみ適用されます、配列のデータ型など`string`と`byte[]`です。

> [!NOTE]  
> Entity Framework には、プロバイダーにデータを渡す前に最大の長さの検証は一切実行されません。 該当する場合を検証するプロバイダーまたはデータ ストアの責任です。 たとえば、基になる列のデータ型として、例外が発生の最大長を超える SQL Server を対象とするときに許可しません余分なデータを格納します。

## <a name="conventions"></a>規則

慣例により、データベース プロバイダーのプロパティの適切なデータ型を選択するまで除外されます。 プロパティで、長さは、データベース プロバイダーをデータ型では、最も長いデータの長さが、通常を選択します。 たとえば、Microsoft SQL Server が使用されます`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`列をキーとして使用する場合)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティの最大長を構成することができます。 この例では、この結果として、SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [MaxLength(500)]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、プロパティの最大長を構成することができます。 この例では、この結果として、SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .HasMaxLength(500);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
