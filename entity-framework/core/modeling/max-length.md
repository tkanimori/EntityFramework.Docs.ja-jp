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
# <a name="maximum-length"></a><span data-ttu-id="f1851-102">最大長</span><span class="sxs-lookup"><span data-stu-id="f1851-102">Maximum Length</span></span>

<span data-ttu-id="f1851-103">最大の長さに設定する特定のプロパティを使用する適切なデータ型のデータ ストアへのヒントを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1851-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="f1851-104">最大長にのみ適用されます、配列のデータ型など`string`と`byte[]`です。</span><span class="sxs-lookup"><span data-stu-id="f1851-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="f1851-105">Entity Framework には、プロバイダーにデータを渡す前に最大の長さの検証は一切実行されません。</span><span class="sxs-lookup"><span data-stu-id="f1851-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="f1851-106">該当する場合を検証するプロバイダーまたはデータ ストアの責任です。</span><span class="sxs-lookup"><span data-stu-id="f1851-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="f1851-107">たとえば、基になる列のデータ型として、例外が発生の最大長を超える SQL Server を対象とするときに許可しません余分なデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="f1851-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="f1851-108">規則</span><span class="sxs-lookup"><span data-stu-id="f1851-108">Conventions</span></span>

<span data-ttu-id="f1851-109">慣例により、データベース プロバイダーのプロパティの適切なデータ型を選択するまで除外されます。</span><span class="sxs-lookup"><span data-stu-id="f1851-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="f1851-110">プロパティで、長さは、データベース プロバイダーをデータ型では、最も長いデータの長さが、通常を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1851-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="f1851-111">たとえば、Microsoft SQL Server が使用されます`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`列をキーとして使用する場合)。</span><span class="sxs-lookup"><span data-stu-id="f1851-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="f1851-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="f1851-112">Data Annotations</span></span>

<span data-ttu-id="f1851-113">データ注釈を使用して、プロパティの最大長を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f1851-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="f1851-114">この例では、この結果として、SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。</span><span class="sxs-lookup"><span data-stu-id="f1851-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [MaxLength(500)]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="f1851-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f1851-115">Fluent API</span></span>

<span data-ttu-id="f1851-116">Fluent API を使用して、プロパティの最大長を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f1851-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="f1851-117">この例では、この結果として、SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。</span><span class="sxs-lookup"><span data-stu-id="f1851-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

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
