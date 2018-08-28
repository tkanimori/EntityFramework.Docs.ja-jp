---
title: 最大の長さ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: e54d3671f378b96a49eaf4cb312e72072813fc6d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996192"
---
# <a name="maximum-length"></a>最大長

最大長を構成する特定のプロパティを使用する適切なデータ型のデータ ストアにヒントを提供します。 最大長にのみ適用されます、配列のデータ型など`string`と`byte[]`します。

> [!NOTE]  
> Entity Framework では、プロバイダーにデータを渡す前に最大の長さの検証は実行しません。 適切な検証するために、プロバイダーまたはデータ ストアの責任です。 たとえば、基になる列のデータ型として例外が発生が最大長を超えると、SQL Server を対象とするときに許可されません余分なデータを格納します。

## <a name="conventions"></a>規約

慣例によりには、データベース プロバイダーのプロパティの適切なデータ型を選択するまでそのままです。 長さを持つプロパティの場合は、データベース プロバイダーはデータ型では、データの最大の長さが一般を選択します。 たとえば、Microsoft SQL Server は使用`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`列がキーとして使用されている場合)。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用して、プロパティの最大長を構成することができます。 この例では、この結果として SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。

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

Fluent API を使用して、プロパティの最大長を構成することができます。 この例では、この結果として SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。

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
