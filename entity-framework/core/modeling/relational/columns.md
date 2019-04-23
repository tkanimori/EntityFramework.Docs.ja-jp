---
title: 列のマッピング - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929863"
---
# <a name="column-mapping"></a><span data-ttu-id="313f7-102">列マッピング</span><span class="sxs-lookup"><span data-stu-id="313f7-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="313f7-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="313f7-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="313f7-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="313f7-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="313f7-105">列マッピングは、列データをからクエリを実行し、データベース内に保存する必要がありますを識別します。</span><span class="sxs-lookup"><span data-stu-id="313f7-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="313f7-106">規約</span><span class="sxs-lookup"><span data-stu-id="313f7-106">Conventions</span></span>

<span data-ttu-id="313f7-107">慣例により、プロパティと同じ名前の列にマップする各プロパティ設定されます。</span><span class="sxs-lookup"><span data-stu-id="313f7-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="313f7-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="313f7-108">Data Annotations</span></span>

<span data-ttu-id="313f7-109">データ注釈を使用して、プロパティがマップされている列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="313f7-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="313f7-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="313f7-110">Fluent API</span></span>

<span data-ttu-id="313f7-111">Fluent API を使用して、プロパティがマップされている列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="313f7-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
