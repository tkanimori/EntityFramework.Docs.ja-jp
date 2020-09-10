---
title: EF インメモリデータベースでのテスト-EF Core
description: EF のメモリ内データベースを使用して Entity Framework Core アプリケーションをテストする
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619395"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="676dc-103">EF インメモリデータベースを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="676dc-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="676dc-104">EF のメモリ内データベースは、多くの場合、リレーショナルデータベースとは動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="676dc-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="676dc-105">「 [EF Core を使用するコードのテスト](xref:core/miscellaneous/testing/index)」で説明されているように、関連する問題とトレードオフについて十分に理解した後は、EF のメモリ内データベースのみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="676dc-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="676dc-106">SQLite はリレーショナルプロバイダーであり、メモリ内のデータベースを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="676dc-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="676dc-107">一般的なリレーショナルデータベースの動作をより厳密に一致させるためにテストに使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="676dc-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="676dc-108">これについては、 [「SQLite を使用した EF Core アプリケーションのテスト](xref:core/miscellaneous/testing/sqlite)」で説明されています。</span><span class="sxs-lookup"><span data-stu-id="676dc-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="676dc-109">このページの情報は、他の場所にあります。</span><span class="sxs-lookup"><span data-stu-id="676dc-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="676dc-110">EF インメモリデータベースでのテストに関する一般的な情報については、「 [EF Core を使用したコードのテスト](xref:core/miscellaneous/testing/index) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="676dc-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="676dc-111">EF のメモリ内データベースを使用するサンプルについては、 [EF Core を使用するアプリケーションをテストする方法を示すサンプル](xref:core/miscellaneous/testing/testing-sample) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="676dc-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="676dc-112">EF インメモリデータベースに関する一般的な情報について [は、「ef インメモリデータベースプロバイダー](xref:core/providers/in-memory/index) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="676dc-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
