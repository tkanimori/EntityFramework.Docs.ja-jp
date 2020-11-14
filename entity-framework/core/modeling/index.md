---
title: モデルの作成と構成 - EF Core
description: Entity Framework Core を使用したモデルの作成と構成の概要
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429612"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="2da4e-103">モデルの作成と構成</span><span class="sxs-lookup"><span data-stu-id="2da4e-103">Creating and configuring a model</span></span>

<span data-ttu-id="2da4e-104">Entity Framework では、一連の規則を利用し、エンティティ クラスの形状に基づいてモデルがビルドされます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="2da4e-105">規則にあるものを捕捉する/オーバーライドする目的で、追加構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="2da4e-106">この記事では、あらゆるデータ ストアをターゲットにするモデルに適用できる構成、あらゆるリレーショナル データベースをターゲットにするときに適用できる構成について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="2da4e-107">プロバイダーも、特定のデータ ストアに固有の構成を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="2da4e-108">プロバイダー固有の構成については、「[データベース プロバイダー](xref:core/providers/index)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2da4e-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="2da4e-109">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="2da4e-110">fluent API を使用してモデルを構成する</span><span class="sxs-lookup"><span data-stu-id="2da4e-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="2da4e-111">派生コンテキストで `OnModelCreating` メソッドをオーバーライドし、`ModelBuilder API` を使用してモデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="2da4e-112">これは最も強力な構成方法であり、エンティティ クラスを変更しなくても構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="2da4e-113">Fluent API 構成には一番上の優先度が与えられ、規則やデータ注釈をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="2da4e-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="2da4e-114">構成のグループ化</span><span class="sxs-lookup"><span data-stu-id="2da4e-114">Grouping configuration</span></span>

<span data-ttu-id="2da4e-115"><xref:System.Data.Entity.DbContext.OnModelCreating%2A> メソッドのサイズを小さくするため、エンティティ型のすべての構成を、<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> を実装する個別のクラスに抽出できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="2da4e-116">その後、`OnModelCreating` から `Configure` メソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="2da4e-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="2da4e-117">特定のアセンブリ内の `IEntityTypeConfiguration` を実装する型で指定されたすべての構成を適用できます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="2da4e-118">構成が適用される順序は定義されていないため、このメソッドは順序が重要でない場合にのみ使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2da4e-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="2da4e-119">データ注釈を使用してモデルを構成する</span><span class="sxs-lookup"><span data-stu-id="2da4e-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="2da4e-120">クラスやプロパティに属性 (データ注釈と呼ばれています) を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="2da4e-121">データ注釈は規則をオーバーライドしますが、Fluent API 構成で上書きされます。</span><span class="sxs-lookup"><span data-stu-id="2da4e-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
