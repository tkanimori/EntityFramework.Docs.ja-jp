---
title: データ型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 26664ebe18abcdeaa2b9c8dc23a6410204f53c8e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197185"
---
# <a name="data-types"></a><span data-ttu-id="596eb-102">データの種類</span><span class="sxs-lookup"><span data-stu-id="596eb-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="596eb-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="596eb-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="596eb-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="596eb-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="596eb-105">データ型は、プロパティがマップされている列のデータベース固有の型を参照します。</span><span class="sxs-lookup"><span data-stu-id="596eb-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="596eb-106">規約</span><span class="sxs-lookup"><span data-stu-id="596eb-106">Conventions</span></span>

<span data-ttu-id="596eb-107">慣例により、データベースプロバイダーは、プロパティの .NET 型に基づいてデータ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="596eb-107">By convention, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="596eb-108">また、構成された[最大長](../max-length.md)、プロパティが主キーの一部であるかどうかなど、他のメタデータも考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="596eb-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="596eb-109">たとえば、SQL Server は、 `datetime2(7)`プロパティの場合は、プロパティの場合は`string` 、キーとして使用されるプロパティの場合は`nvarchar(450)`を使用`DateTime` `nvarchar(max)` `string`します。</span><span class="sxs-lookup"><span data-stu-id="596eb-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="596eb-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="596eb-110">Data Annotations</span></span>

<span data-ttu-id="596eb-111">データ注釈を使用して、列のデータ型を正確に指定できます。</span><span class="sxs-lookup"><span data-stu-id="596eb-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="596eb-112">たとえば、次のコードは`Url` 、の最大`200`長`Rating`を持つ非 unicode 文字列としてを構成し、 `5`の有効桁数`2`と小数点以下桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="596eb-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="596eb-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="596eb-113">Fluent API</span></span>

<span data-ttu-id="596eb-114">また、Fluent API を使用して、列に同じデータ型を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="596eb-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DataType.cs?name=Model&highlight=9-10)]
