---
title: データ型 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 9060f66c752be01090ce40be6bf3a32f348ce571
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993522"
---
# <a name="data-types"></a><span data-ttu-id="a6432-102">データの種類</span><span class="sxs-lookup"><span data-stu-id="a6432-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="a6432-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6432-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="a6432-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a6432-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="a6432-105">データ型は、データベースの特定の種類のプロパティがマップされている列を指します。</span><span class="sxs-lookup"><span data-stu-id="a6432-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="a6432-106">規約</span><span class="sxs-lookup"><span data-stu-id="a6432-106">Conventions</span></span>

<span data-ttu-id="a6432-107">慣例により、データベース プロバイダーは、プロパティの CLR 型に基づいたデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="a6432-107">By convention, the database provider selects a data type based on the CLR type of the property.</span></span> <span data-ttu-id="a6432-108">アカウントにもかかるなど、構成されたその他のメタデータ[最大長](../max-length.md)プロパティが主キーなどの一部であるかどうか。</span><span class="sxs-lookup"><span data-stu-id="a6432-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="a6432-109">たとえば、SQL Server を使用して`datetime2(7)`の`DateTime`プロパティ、および`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`の`string`キーとして使用されるプロパティ)。</span><span class="sxs-lookup"><span data-stu-id="a6432-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a6432-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="a6432-110">Data Annotations</span></span>

<span data-ttu-id="a6432-111">データ注釈を使用して、列の正確なデータの種類を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a6432-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="a6432-112">たとえば、次のコードでは構成`Url`の最大長の unicode 以外の文字列として`200`と`Rating`で有効桁数の 10 進数として`5`のスケールと`2`します。</span><span class="sxs-lookup"><span data-stu-id="a6432-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="a6432-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a6432-113">Fluent API</span></span>

<span data-ttu-id="a6432-114">列の同じデータ型を指定するのに、Fluent API を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a6432-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
