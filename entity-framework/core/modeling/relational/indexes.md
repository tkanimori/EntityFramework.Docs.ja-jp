---
title: "インデックスの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
ms.technology: entity-framework-core
uid: core/modeling/relational/indexes
ms.openlocfilehash: f577fccfefc6908edf2ac47ae630323d7a9f5f2b
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="indexes"></a><span data-ttu-id="cf210-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="cf210-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="cf210-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf210-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="cf210-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf210-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="cf210-105">リレーショナル データベース内のインデックスは、Entity Framework のコアでのインデックスと同じ概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="cf210-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="cf210-106">規約</span><span class="sxs-lookup"><span data-stu-id="cf210-106">Conventions</span></span>

<span data-ttu-id="cf210-107">慣例により、インデックスが名前付き`IX_<type name>_<property name>`します。</span><span class="sxs-lookup"><span data-stu-id="cf210-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="cf210-108">複合インデックスの`<property name>`プロパティ名のアンダー スコアの区切られたリストになります。</span><span class="sxs-lookup"><span data-stu-id="cf210-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="cf210-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="cf210-109">Data Annotations</span></span>

<span data-ttu-id="cf210-110">インデックスは、データ注釈を使用していない構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf210-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="cf210-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="cf210-111">Fluent API</span></span>

<span data-ttu-id="cf210-112">Fluent API を使用すると、インデックスの名前を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf210-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="cf210-113">フィルターを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf210-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="cf210-114">SQL Server プロバイダー EF を使用して追加したときに、' は NOT NULL' は一意のインデックスの一部であるすべての null 許容列のフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="cf210-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="cf210-115">この規則を指定することができますを上書きする、`null`値。</span><span class="sxs-lookup"><span data-stu-id="cf210-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]
