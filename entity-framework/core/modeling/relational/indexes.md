---
title: インデックス - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 605b30ce710d9034deab97f695496ec66a576565
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993218"
---
# <a name="indexes"></a><span data-ttu-id="d4372-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="d4372-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="d4372-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4372-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="d4372-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d4372-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="d4372-105">リレーショナル データベース内のインデックスは、Entity framework core でのインデックスと同じ概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="d4372-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="d4372-106">規約</span><span class="sxs-lookup"><span data-stu-id="d4372-106">Conventions</span></span>

<span data-ttu-id="d4372-107">慣例により、インデックスの名前は`IX_<type name>_<property name>`します。</span><span class="sxs-lookup"><span data-stu-id="d4372-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="d4372-108">複合インデックスの`<property name>`はプロパティ名、アンダー スコア区切りリストになります。</span><span class="sxs-lookup"><span data-stu-id="d4372-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d4372-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="d4372-109">Data Annotations</span></span>

<span data-ttu-id="d4372-110">データ注釈を使用してインデックスを構成しないことができます。</span><span class="sxs-lookup"><span data-stu-id="d4372-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="d4372-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d4372-111">Fluent API</span></span>

<span data-ttu-id="d4372-112">Fluent API を使用すると、インデックスの名前を構成します。</span><span class="sxs-lookup"><span data-stu-id="d4372-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="d4372-113">フィルターを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d4372-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="d4372-114">SQL Server プロバイダー EF を使用して追加するときは、一意のインデックスの一部であるすべての null 許容列の ' IS NOT NULL' フィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="d4372-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="d4372-115">この規則を指定することができますを上書きする、`null`値。</span><span class="sxs-lookup"><span data-stu-id="d4372-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
