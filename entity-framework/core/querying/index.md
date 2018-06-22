---
title: データのクエリ - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
ms.technology: entity-framework-core
uid: core/querying/index
ms.openlocfilehash: a2dd830b25c64b007a881c105a87b5c631b00266
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26048884"
---
# <a name="querying-data"></a><span data-ttu-id="7ab62-102">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="7ab62-102">Querying Data</span></span>

<span data-ttu-id="7ab62-103">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="7ab62-103">Entity Framework Core uses Language Integrate Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="7ab62-104">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="7ab62-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span> <span data-ttu-id="7ab62-105">LINQ クエリの表現がデータベース プロバイダーに渡され、データベース固有のクエリ言語 (たとえば、リレーショナル データベースの場合は SQL) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="7ab62-105">A representation of the LINQ query is passed to the database provider, to be translated in database-specific query language (e.g. SQL for a relational database).</span></span> <span data-ttu-id="7ab62-106">クエリが処理されるしくみについては、「[クエリのしくみ](overview.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ab62-106">For more detailed information on how a query is processed, see [How Query Works](overview.md).</span></span>
