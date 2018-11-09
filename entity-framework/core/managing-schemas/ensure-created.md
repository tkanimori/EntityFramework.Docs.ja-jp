---
title: 作成し、Drop Api - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/10/2017
ms.openlocfilehash: 336f6fd655603a2474a58dfef377e121d9b04c3a
ms.sourcegitcommit: a088421ecac4f5dc5213208170490181ae2f5f0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285640"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="7e984-102">作成し、Drop Api</span><span class="sxs-lookup"><span data-stu-id="7e984-102">Create and Drop APIs</span></span>

<span data-ttu-id="7e984-103">EnsureCreated と EnsureDeleted メソッドに軽量な代替手段を提供[移行](migrations/index.md)データベース スキーマを管理するためです。</span><span class="sxs-lookup"><span data-stu-id="7e984-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="7e984-104">これは、機能は、データが一時的であり、スキーマが変更されたときに削除することができる場合のシナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="7e984-104">This is useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="7e984-105">プロトタイプ作成、テストでは、またはのローカル キャッシュたとえば中です。</span><span class="sxs-lookup"><span data-stu-id="7e984-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="7e984-106">一部のプロバイダー (特に非リレーショナル) は、移行でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7e984-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="7e984-107">これらの場合は、EnsureCreated と、データベース スキーマを初期化するために最も簡単な方法では多くの場合です。</span><span class="sxs-lookup"><span data-stu-id="7e984-107">For these, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="7e984-108">EnsureCreated と移行は、うまく連携機能しません。</span><span class="sxs-lookup"><span data-stu-id="7e984-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="7e984-109">移行を使用している場合は、スキーマを初期化するために EnsureCreated を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="7e984-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="7e984-110">EnsureCreated からの移行への移行は、シームレスなエクスペリエンスではありません。</span><span class="sxs-lookup"><span data-stu-id="7e984-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="7e984-111">これを実現する simpelest 方法、データベースを削除し、Migrations を使用して再作成です。</span><span class="sxs-lookup"><span data-stu-id="7e984-111">The simpelest way to achieve this is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="7e984-112">Migrations を使用して、将来を予測する場合は、EnsureCreated を使用する代わりに移行から始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7e984-112">If you anticipate using Migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="7e984-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="7e984-113">EnsureDeleted</span></span>

<span data-ttu-id="7e984-114">存在する場合、EnsureDeleted メソッドは、データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="7e984-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="7e984-115">適切なアクセス許可を持っていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="7e984-115">If you don't have the appropiate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="7e984-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="7e984-116">EnsureCreated</span></span>

<span data-ttu-id="7e984-117">存在しないし、データベース スキーマを初期化する場合、EnsureCreated は、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e984-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="7e984-118">任意のテーブルが存在しない場合 (他の DbContext クラスでテーブルを含む)、スキーマはを初期化しません。</span><span class="sxs-lookup"><span data-stu-id="7e984-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="7e984-119">これらのメソッドの非同期バージョンも使用できます。</span><span class="sxs-lookup"><span data-stu-id="7e984-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="7e984-120">SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="7e984-120">SQL Script</span></span>

<span data-ttu-id="7e984-121">EnsureCreated で使用される SQL を取得するには、GenerateCreateScript メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7e984-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="7e984-122">複数の DbContext クラス</span><span class="sxs-lookup"><span data-stu-id="7e984-122">Multiple DbContext classes</span></span>

<span data-ttu-id="7e984-123">EnsureCreated では、テーブルがデータベースに存在しない場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="7e984-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="7e984-124">必要に応じて、して初期化するのには、スキーマが必要なかどうかは、独自のチェックを記述し、基になる IRelationalDatabaseCreator サービスを使用してスキーマを初期化します。</span><span class="sxs-lookup"><span data-stu-id="7e984-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
