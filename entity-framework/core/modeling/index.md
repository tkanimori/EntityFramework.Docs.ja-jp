---
title: モデルの作成 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: 78a8ffd2393a914edf737104f14e41f8a9074ad5
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929899"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="0ef3e-102">モデルの作成と構成</span><span class="sxs-lookup"><span data-stu-id="0ef3e-102">Creating and configuring a Model</span></span>

<span data-ttu-id="0ef3e-103">Entity Framework では、一連の規則を利用し、エンティティ クラスの形状に基づいてモデルがビルドされます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-103">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="0ef3e-104">規則にあるものを捕捉する/オーバーライドする目的で、追加構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-104">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="0ef3e-105">この記事では、あらゆるデータ ストアをターゲットにするモデルに適用できる構成、あらゆるリレーショナル データベースをターゲットにするときに適用できる構成について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-105">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="0ef3e-106">プロバイダーも、特定のデータ ストアに固有の構成を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-106">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="0ef3e-107">プロバイダー固有の構成については、「 [データベース プロバイダー](../providers/index.md) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-107">For documentation on provider specific configuration see the [Database Providers](../providers/index.md) section.</span></span>

> [!TIP]  
> <span data-ttu-id="0ef3e-108">この記事の [サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-108">You can view this article’s [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="0ef3e-109">fluent API を使用してモデルを構成する</span><span class="sxs-lookup"><span data-stu-id="0ef3e-109">Use fluent API to configure a model</span></span>

<span data-ttu-id="0ef3e-110">派生コンテキストで  `OnModelCreating`  メソッドをオーバーライドし、 `ModelBuilder API` を使用してモデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-110">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="0ef3e-111">これは最も強力な構成方法であり、エンティティ クラスを変更しなくても構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-111">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="0ef3e-112">Fluent API 構成には一番上の優先度が与えられ、規則やデータ注釈をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-112">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="0ef3e-113">データ注釈を使用してモデルを構成する</span><span class="sxs-lookup"><span data-stu-id="0ef3e-113">Use data annotations to configure a model</span></span>

<span data-ttu-id="0ef3e-114">クラスやプロパティに属性 (データ注釈と呼ばれています) を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-114">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="0ef3e-115">データ注釈は規則をオーバーライドしますが、Fluent API 構成で上書きされます。</span><span class="sxs-lookup"><span data-stu-id="0ef3e-115">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]
