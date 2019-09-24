---
title: インデックス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: 7dcf27dedbde45302a462a4c41a811b9868e40bb
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197016"
---
# <a name="indexes"></a><span data-ttu-id="268d8-102">Indexes</span><span class="sxs-lookup"><span data-stu-id="268d8-102">Indexes</span></span>

> [!NOTE]  
> <span data-ttu-id="268d8-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="268d8-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="268d8-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="268d8-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="268d8-105">リレーショナルデータベースのインデックスは、Entity Framework の中核となるインデックスと同じ概念にマップされます。</span><span class="sxs-lookup"><span data-stu-id="268d8-105">An index in a relational database maps to the same concept as an index in the core of Entity Framework.</span></span>

## <a name="conventions"></a><span data-ttu-id="268d8-106">規約</span><span class="sxs-lookup"><span data-stu-id="268d8-106">Conventions</span></span>

<span data-ttu-id="268d8-107">規則により、インデックスに`IX_<type name>_<property name>`はという名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="268d8-107">By convention, indexes are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="268d8-108">複合インデックス`<property name>`の場合は、アンダースコアで区切られたプロパティ名のリストになります。</span><span class="sxs-lookup"><span data-stu-id="268d8-108">For composite indexes `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="268d8-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="268d8-109">Data Annotations</span></span>

<span data-ttu-id="268d8-110">データ注釈を使用してインデックスを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="268d8-110">Indexes can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="268d8-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="268d8-111">Fluent API</span></span>

<span data-ttu-id="268d8-112">Fluent API を使用して、インデックスの名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="268d8-112">You can use the Fluent API to configure the name of an index.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexName.cs?name=Model&highlight=9)]

<span data-ttu-id="268d8-113">フィルターを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="268d8-113">You can also specify a filter.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexFilter.cs?name=Model&highlight=9)]

<span data-ttu-id="268d8-114">SQL Server プロバイダー EF を使用すると、一意インデックスの一部である null 許容型のすべての列に対して ' IS NOT NULL ' フィルターが追加されます。</span><span class="sxs-lookup"><span data-stu-id="268d8-114">When using the SQL Server provider EF adds a 'IS NOT NULL' filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="268d8-115">この規則をオーバーライドするには、 `null`値を指定します。</span><span class="sxs-lookup"><span data-stu-id="268d8-115">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a><span data-ttu-id="268d8-116">SQL Server インデックスに列を含める</span><span class="sxs-lookup"><span data-stu-id="268d8-116">Include Columns in SQL Server Indexes</span></span>

<span data-ttu-id="268d8-117">[付加列を使用](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)してインデックスを構成すると、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合に、クエリのパフォーマンスを大幅に向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="268d8-117">You can configure [indexes with included columns](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns) to significantly improve query performance when all columns in the query are included in the index as key or non-key columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ForSqlServerHasIndex.cs?name=Model)]
