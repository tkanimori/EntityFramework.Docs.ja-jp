---
title: キー (プライマリ)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 66c64c389294e8e109a614a2bea8311932660dea
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655943"
---
# <a name="keys-primary"></a><span data-ttu-id="e8d72-102">キー (主)</span><span class="sxs-lookup"><span data-stu-id="e8d72-102">Keys (primary)</span></span>

<span data-ttu-id="e8d72-103">キーは、各エンティティインスタンスのプライマリ一意識別子として機能します。</span><span class="sxs-lookup"><span data-stu-id="e8d72-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="e8d72-104">リレーショナルデータベースを使用する場合、これは*主キー*の概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="e8d72-105">また、主キーではない一意の識別子を構成することもできます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="e8d72-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<span data-ttu-id="e8d72-106">次のいずれかの方法を使用して、主キーの設定/作成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-106">One of the following methods can be used to setup/create a primary key.</span></span>

## <a name="conventions"></a><span data-ttu-id="e8d72-107">規約</span><span class="sxs-lookup"><span data-stu-id="e8d72-107">Conventions</span></span>

<span data-ttu-id="e8d72-108">慣例により、`Id` または `<type name>Id` という名前のプロパティは、エンティティのキーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-108">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyIdhighlight=3)]

## <a name="data-annotations"></a><span data-ttu-id="e8d72-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="e8d72-109">Data Annotations</span></span>

<span data-ttu-id="e8d72-110">データ注釈を使用して、1つのプロパティをエンティティのキーとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-110">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="e8d72-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e8d72-111">Fluent API</span></span>

<span data-ttu-id="e8d72-112">Fluent API を使用して、1つのプロパティをエンティティのキーとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-112">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

<span data-ttu-id="e8d72-113">また、Fluent API を使用して、エンティティ (複合キーと呼ばれます) のキーとして複数のプロパティを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e8d72-113">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="e8d72-114">複合キーは、Fluent API を使用してのみ構成できます。複合キーは設定されず、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="e8d72-114">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
