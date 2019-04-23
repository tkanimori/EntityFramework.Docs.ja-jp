---
title: 最大の長さ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: 3220518cb0a409b6e802d2f3a98acdb949ffbf56
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929850"
---
# <a name="maximum-length"></a><span data-ttu-id="29c2c-102">最大長</span><span class="sxs-lookup"><span data-stu-id="29c2c-102">Maximum Length</span></span>

<span data-ttu-id="29c2c-103">最大長を構成する特定のプロパティを使用する適切なデータ型のデータ ストアにヒントを提供します。</span><span class="sxs-lookup"><span data-stu-id="29c2c-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="29c2c-104">最大長にのみ適用されます、配列のデータ型など`string`と`byte[]`します。</span><span class="sxs-lookup"><span data-stu-id="29c2c-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="29c2c-105">Entity Framework では、プロバイダーにデータを渡す前に最大の長さの検証は実行しません。</span><span class="sxs-lookup"><span data-stu-id="29c2c-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="29c2c-106">適切な検証するために、プロバイダーまたはデータ ストアの責任です。</span><span class="sxs-lookup"><span data-stu-id="29c2c-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="29c2c-107">たとえば、基になる列のデータ型として例外が発生が最大長を超えると、SQL Server を対象とするときに許可されません余分なデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="29c2c-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="29c2c-108">規約</span><span class="sxs-lookup"><span data-stu-id="29c2c-108">Conventions</span></span>

<span data-ttu-id="29c2c-109">慣例によりには、データベース プロバイダーのプロパティの適切なデータ型を選択するまでそのままです。</span><span class="sxs-lookup"><span data-stu-id="29c2c-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="29c2c-110">長さを持つプロパティの場合は、データベース プロバイダーはデータ型では、データの最大の長さが一般を選択します。</span><span class="sxs-lookup"><span data-stu-id="29c2c-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="29c2c-111">たとえば、Microsoft SQL Server は使用`nvarchar(max)`の`string`プロパティ (または`nvarchar(450)`列がキーとして使用されている場合)。</span><span class="sxs-lookup"><span data-stu-id="29c2c-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="29c2c-112">データの注釈</span><span class="sxs-lookup"><span data-stu-id="29c2c-112">Data Annotations</span></span>

<span data-ttu-id="29c2c-113">データ注釈を使用して、プロパティの最大長を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="29c2c-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="29c2c-114">この例では、この結果として SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。</span><span class="sxs-lookup"><span data-stu-id="29c2c-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="29c2c-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="29c2c-115">Fluent API</span></span>

<span data-ttu-id="29c2c-116">Fluent API を使用して、プロパティの最大長を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="29c2c-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="29c2c-117">この例では、この結果として SQL Server を対象とする、`nvarchar(500)`使用されているデータ型。</span><span class="sxs-lookup"><span data-stu-id="29c2c-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=11-13)]
