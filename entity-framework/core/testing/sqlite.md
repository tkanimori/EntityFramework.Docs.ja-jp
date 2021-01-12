---
title: SQLite を使用したテスト-EF Core
description: SQLite を使用した Entity Framework Core アプリケーションのテスト
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: da2504cfe7997a10a5ee8c447b1c6ef00dd02369
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129058"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="e2f00-103">SQLite を使用した EF Core アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="e2f00-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="e2f00-104">SQLite を使用すると、EF Core アプリケーションをテストする効果的な方法になります。</span><span class="sxs-lookup"><span data-stu-id="e2f00-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="e2f00-105">ただし、SQLite の動作が他のデータベースシステムと異なる場合は、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2f00-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span>
> <span data-ttu-id="e2f00-106">問題とトレードオフの詳細については、「 [EF Core を使用したコードのテスト](xref:core/testing/index) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e2f00-106">See [Testing code that uses EF Core](xref:core/testing/index) for a discussion of the issues and trade-offs.</span></span>

<span data-ttu-id="e2f00-107">このドキュメントでは、 [EF Core を使用するアプリケーションのテスト方法を示すサンプル](xref:core/testing/testing-sample)で導入された概念について説明します。</span><span class="sxs-lookup"><span data-stu-id="e2f00-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample).</span></span>
<span data-ttu-id="e2f00-108">このサンプルのコード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e2f00-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="e2f00-109">SQLite インメモリデータベースの使用</span><span class="sxs-lookup"><span data-stu-id="e2f00-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="e2f00-110">通常、SQLite は単純なファイルとしてデータベースを作成し、アプリケーションでインプロセスのファイルにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e2f00-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="e2f00-111">これは、特に高速 [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)を使用する場合には非常に高速です。</span><span class="sxs-lookup"><span data-stu-id="e2f00-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span>

<span data-ttu-id="e2f00-112">SQLite では、単にメモリ内で作成されたデータベースを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="e2f00-113">これは、インメモリデータベースの有効期間を理解していれば、EF Core で簡単に使用できます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>

* <span data-ttu-id="e2f00-114">データベースへの接続が開かれると、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="e2f00-115">データベースへの接続が閉じられると、データベースは削除されます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="e2f00-116">指定されている場合、既に開いている接続が使用されるため、EF Core は閉じようとしません。</span><span class="sxs-lookup"><span data-stu-id="e2f00-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="e2f00-117">そのため、メモリ内の SQLite データベースで EF Core を使用するには、EF に渡す前に接続を開く必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2f00-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>

<span data-ttu-id="e2f00-118">この [サンプル](xref:core/testing/testing-sample) では、次のコードを使用してこれを実現します。</span><span class="sxs-lookup"><span data-stu-id="e2f00-118">The [sample](xref:core/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="e2f00-119">注意:</span><span class="sxs-lookup"><span data-stu-id="e2f00-119">Notice:</span></span>

* <span data-ttu-id="e2f00-120">メソッドは、 `CreateInMemoryDatabase` SQLite メモリ内データベースを作成し、そのデータベースへの接続を開きます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="e2f00-121">作成されたはから抽出され、 `DbConnection` `ContextOptions` 保存されます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="e2f00-122">リソースがリークしないようにテストが破棄されると、接続は破棄されます。</span><span class="sxs-lookup"><span data-stu-id="e2f00-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span>

> [!NOTE]
> <span data-ttu-id="e2f00-123">[問題 #16103](https://github.com/dotnet/efcore/issues/16103) は、この接続管理を容易にする方法を追跡することです。</span><span class="sxs-lookup"><span data-stu-id="e2f00-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span>
