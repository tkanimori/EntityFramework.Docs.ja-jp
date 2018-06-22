---
title: データ型の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
ms.technology: entity-framework-core
uid: core/modeling/relational/data-types
ms.openlocfilehash: fd4668a3f9554eb9d3b1161d5dddce2fcdcac712
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053502"
---
# <a name="data-types"></a><span data-ttu-id="6a33c-102">データ型</span><span class="sxs-lookup"><span data-stu-id="6a33c-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="6a33c-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6a33c-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="6a33c-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="6a33c-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="6a33c-105">データ型は、データベースの特定の種類のプロパティがマップされている列を参照します。</span><span class="sxs-lookup"><span data-stu-id="6a33c-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="6a33c-106">規則</span><span class="sxs-lookup"><span data-stu-id="6a33c-106">Conventions</span></span>

<span data-ttu-id="6a33c-107">慣例により、データベース プロバイダーは、プロパティの CLR 型に基づいたデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a33c-107">By convention, the database provider selects a data type based on the CLR type of the property.</span></span> <span data-ttu-id="6a33c-108">アカウントにもかかるなど、構成されたその他のメタデータ[最大長](../max-length.md)プロパティが主キーなどの一部であるかどうか、します。</span><span class="sxs-lookup"><span data-stu-id="6a33c-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="6a33c-109">たとえば、SQL Server を使用して`datetime2(7)`の`DateTime`プロパティ、および`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`の`string`キーとして使用されるプロパティ)。</span><span class="sxs-lookup"><span data-stu-id="6a33c-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="6a33c-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6a33c-110">Data Annotations</span></span>

<span data-ttu-id="6a33c-111">データ注釈を使用して、正確なデータ型の列を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="6a33c-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="6a33c-112">たとえば、次のコード構成`Url`の最大長を持つ非 unicode 文字列として`200`と`Rating`の有効桁数と小数点`5`の拡大/縮小`2`です。</span><span class="sxs-lookup"><span data-stu-id="6a33c-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="6a33c-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6a33c-113">Fluent API</span></span>

<span data-ttu-id="6a33c-114">Fluent API を使用して、同じデータ型の列を指定することができますも。</span><span class="sxs-lookup"><span data-stu-id="6a33c-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
