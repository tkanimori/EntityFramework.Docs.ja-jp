---
title: データのクエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 51aaa5de11d3fe38b4fba82db8dcb5658088cc27
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993535"
---
# <a name="querying-data"></a><span data-ttu-id="8e065-102">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="8e065-102">Querying Data</span></span>

<span data-ttu-id="8e065-103">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="8e065-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="8e065-104">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="8e065-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span> <span data-ttu-id="8e065-105">LINQ クエリの表現がデータベース プロバイダーに渡され、データベース固有のクエリ言語 (たとえば、リレーショナル データベースの SQL) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8e065-105">A representation of the LINQ query is passed to the database provider, to be translated in database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="8e065-106">クエリが処理されるしくみについては、「[クエリのしくみ](overview.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e065-106">For more detailed information on how a query is processed, see [How Query Works](overview.md).</span></span>
