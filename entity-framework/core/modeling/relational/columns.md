---
title: 列マッピング-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: eaffc0cc1642f64edabeeef974f5f6de7a23b656
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197205"
---
# <a name="column-mapping"></a><span data-ttu-id="be1aa-102">列マッピング</span><span class="sxs-lookup"><span data-stu-id="be1aa-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="be1aa-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="be1aa-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="be1aa-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="be1aa-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="be1aa-105">列マッピングでは、データベースのどの列データに対してクエリを行い、保存するかを指定します。</span><span class="sxs-lookup"><span data-stu-id="be1aa-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="be1aa-106">規約</span><span class="sxs-lookup"><span data-stu-id="be1aa-106">Conventions</span></span>

<span data-ttu-id="be1aa-107">慣例により、各プロパティは、プロパティと同じ名前の列にマップされるように設定されます。</span><span class="sxs-lookup"><span data-stu-id="be1aa-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="be1aa-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="be1aa-108">Data Annotations</span></span>

<span data-ttu-id="be1aa-109">データ注釈を使用して、プロパティがマップされる列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="be1aa-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="be1aa-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="be1aa-110">Fluent API</span></span>

<span data-ttu-id="be1aa-111">Fluent API を使用して、プロパティがマップされる列を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="be1aa-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Column.cs?highlight=11-13)]
