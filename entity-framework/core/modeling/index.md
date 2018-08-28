---
title: モデルの作成 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: 9f702d5833b88e6eb77c0afefdae0ed3bc162ec8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993934"
---
# <a name="creating-a-model"></a>モデルの作成

Entity Framework では、一連の規則を利用し、エンティティ クラスの形状に基づいてモデルがビルドされます。 規則にあるものを捕捉する/オーバーライドする目的で、追加構成を指定できます。

この記事では、あらゆるデータ ストアをターゲットにするモデルに適用できる構成、あらゆるリレーショナル データベースをターゲットにするときに適用できる構成について取り上げます。 プロバイダーも、特定のデータ ストアに固有の構成を有効にできます。 プロバイダー固有の構成については、「[データベース プロバイダー](../providers/index.md)」セクションを参照してください。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples)は GitHub で確認できます。

## <a name="methods-of-configuration"></a>構成の方法

### <a name="fluent-api"></a>Fluent API

派生コンテキストで `OnModelCreating` メソッドをオーバーライドし、`ModelBuilder API` を使用してモデルを構成できます。 これは最も強力な構成方法であり、エンティティ クラスを変更しなくても構成を指定できます。 Fluent API 構成には一番上の優先度が与えられ、規則やデータ注釈をオーバーライドします。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?range=5-15&highlight=5-10)] -->

``` csharp
    class MyContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>()
                .Property(b => b.Url)
                .IsRequired();
        }
    }
```

### <a name="data-annotations"></a>データの注釈

クラスやプロパティに属性 (データ注釈と呼ばれています) を適用することもできます。 データ注釈は規則をオーバーライドしますが、Fluent API 構成に上書きされます。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?range=11-16&highlight=4)] -->
``` csharp
    public class Blog
    {
        public int BlogId { get; set; }
        [Required]
        public string Url { get; set; }
    }
```
