---
title: カスタム移行操作-EF Core
description: Entity Framework Core を使用したデータベーススキーマ管理のためのカスタムおよび生の SQL 移行の管理
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429833"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="02f20-103">カスタム移行操作</span><span class="sxs-lookup"><span data-stu-id="02f20-103">Custom Migrations Operations</span></span>

<span data-ttu-id="02f20-104">MigrationBuilder API を使用すると、移行中にさまざまな種類の操作を実行できますが、これは包括的なものではありません。</span><span class="sxs-lookup"><span data-stu-id="02f20-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="02f20-105">ただし、API を拡張して、独自の操作を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="02f20-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="02f20-106">API を拡張するには、メソッドを使用する方法と、 `Sql()` カスタムオブジェクトを定義する方法の2つの方法があります。 `MigrationOperation`</span><span class="sxs-lookup"><span data-stu-id="02f20-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="02f20-107">例として、各方法を使用してデータベースユーザーを作成する操作を実装する方法を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="02f20-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="02f20-108">この移行では、次のコードを記述できるようにします。</span><span class="sxs-lookup"><span data-stu-id="02f20-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="02f20-109">MigrationBuilder () の使用</span><span class="sxs-lookup"><span data-stu-id="02f20-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="02f20-110">カスタム操作を実装する最も簡単な方法は、を呼び出す拡張メソッドを定義することです `MigrationBuilder.Sql()` 。</span><span class="sxs-lookup"><span data-stu-id="02f20-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="02f20-111">次に、適切な Transact-sql を生成する例を示します。</span><span class="sxs-lookup"><span data-stu-id="02f20-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="02f20-112">ステートメントが `EXEC` SQL バッチ内の最初のステートメントまたは1つのステートメントである必要がある場合は、関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="02f20-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="02f20-113">参照されている列がテーブルに存在しない場合に発生する可能性があるべき等移行スクリプトで、パーサーエラーを回避することが必要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="02f20-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="02f20-114">移行で複数のデータベースプロバイダーをサポートする必要がある場合は、プロパティを使用でき `MigrationBuilder.ActiveProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="02f20-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="02f20-115">Microsoft SQL Server と PostgreSQL の両方をサポートする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="02f20-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="02f20-116">この方法は、カスタム操作が適用されるすべてのプロバイダーがわかっている場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="02f20-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="02f20-117">MigrationOperation を使用する</span><span class="sxs-lookup"><span data-stu-id="02f20-117">Using a MigrationOperation</span></span>

<span data-ttu-id="02f20-118">SQL からカスタム操作を分離するには、独自のを定義してそれを表すことができ `MigrationOperation` ます。</span><span class="sxs-lookup"><span data-stu-id="02f20-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="02f20-119">次に、操作がプロバイダーに渡され、生成する適切な SQL を決定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="02f20-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="02f20-120">この方法では、拡張メソッドでこれらの操作のいずれかをに追加するだけで済み `MigrationBuilder.Operations` ます。</span><span class="sxs-lookup"><span data-stu-id="02f20-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="02f20-121">この方法では、各プロバイダーがサービスでこの操作の SQL を生成する方法を知る必要があり `IMigrationsSqlGenerator` ます。</span><span class="sxs-lookup"><span data-stu-id="02f20-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="02f20-122">新しい操作を処理するために SQL Server のジェネレーターをオーバーライドする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="02f20-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="02f20-123">既定の移行 sql ジェネレーターサービスを更新されたものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="02f20-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
