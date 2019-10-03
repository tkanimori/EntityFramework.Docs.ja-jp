---
title: Api の作成と削除-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
ms.openlocfilehash: 88c1403d2fae740ad78bb7c41d404b0dd91e86ae
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813441"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="1663e-102">Create API と Drop API</span><span class="sxs-lookup"><span data-stu-id="1663e-102">Create and Drop APIs</span></span>

<span data-ttu-id="1663e-103">EnsureCreated メソッドと EnsureDeleted メソッドは、データベーススキーマを管理するための[移行](migrations/index.md)のための軽量な代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="1663e-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="1663e-104">これらのメソッドは、データが一時的なもので、スキーマが変更されたときに削除できる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1663e-104">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="1663e-105">たとえば、プロトタイプの場合、テストの場合は、ローカルキャッシュの場合はです。</span><span class="sxs-lookup"><span data-stu-id="1663e-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="1663e-106">一部のプロバイダー (特に非リレーショナル) は、移行をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="1663e-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="1663e-107">これらのプロバイダーでは、多くの場合、EnsureCreated はデータベーススキーマを初期化する最も簡単な方法です。</span><span class="sxs-lookup"><span data-stu-id="1663e-107">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="1663e-108">EnsureCreated と移行はうまく連携しません。</span><span class="sxs-lookup"><span data-stu-id="1663e-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="1663e-109">移行を使用している場合は、EnsureCreated を使用してスキーマを初期化しないでください。</span><span class="sxs-lookup"><span data-stu-id="1663e-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="1663e-110">EnsureCreated から移行への移行は、シームレスなエクスペリエンスではありません。</span><span class="sxs-lookup"><span data-stu-id="1663e-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="1663e-111">最も簡単な方法は、データベースを削除し、移行を使用してデータベースを再作成することです。</span><span class="sxs-lookup"><span data-stu-id="1663e-111">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="1663e-112">今後移行を使用する予定がある場合は、EnsureCreated を使用する代わりに、移行を開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1663e-112">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="1663e-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="1663e-113">EnsureDeleted</span></span>

<span data-ttu-id="1663e-114">EnsureDeleted メソッドは、データベースが存在する場合、そのデータベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="1663e-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="1663e-115">適切なアクセス許可がない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="1663e-115">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="1663e-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="1663e-116">EnsureCreated</span></span>

<span data-ttu-id="1663e-117">データベースが存在しない場合は、EnsureCreated によって作成され、データベーススキーマが初期化されます。</span><span class="sxs-lookup"><span data-stu-id="1663e-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="1663e-118">テーブルが存在する場合 (別の DbContext クラスのテーブルを含む)、スキーマは初期化されません。</span><span class="sxs-lookup"><span data-stu-id="1663e-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="1663e-119">これらのメソッドの非同期バージョンも使用できます。</span><span class="sxs-lookup"><span data-stu-id="1663e-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="1663e-120">SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="1663e-120">SQL Script</span></span>

<span data-ttu-id="1663e-121">EnsureCreated によって使用される SQL を取得するには、GenerateCreateScript メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1663e-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="1663e-122">複数の DbContext クラス</span><span class="sxs-lookup"><span data-stu-id="1663e-122">Multiple DbContext classes</span></span>

<span data-ttu-id="1663e-123">EnsureCreated は、データベース内にテーブルが存在しない場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1663e-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="1663e-124">必要に応じて、独自のチェックを記述してスキーマを初期化する必要があるかどうかを確認し、基になる IRelationalDatabaseCreator サービスを使用してスキーマを初期化できます。</span><span class="sxs-lookup"><span data-stu-id="1663e-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
