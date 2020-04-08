---
title: Entity Framework Core の概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e6127f775d6bbbdf81debf5519388fe252fe079d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78412837"
---
# <a name="entity-framework-core"></a><span data-ttu-id="f7931-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f7931-102">Entity Framework Core</span></span>

<span data-ttu-id="f7931-103">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="f7931-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="f7931-104">EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。</span><span class="sxs-lookup"><span data-stu-id="f7931-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="f7931-105">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7931-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="f7931-106">モデル</span><span class="sxs-lookup"><span data-stu-id="f7931-106">The Model</span></span>

<span data-ttu-id="f7931-107">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f7931-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="f7931-108">モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="f7931-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="f7931-109">詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7931-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="f7931-110">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、[EF 移行](managing-schemas/migrations/index.md)を使ってモデルからデータベースを作成してから、モデルが時間の経過と共に変化するのに合わせてそれを進化させたりできます。</span><span class="sxs-lookup"><span data-stu-id="f7931-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="f7931-111">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="f7931-111">Querying</span></span>

<span data-ttu-id="f7931-112">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="f7931-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="f7931-113">詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7931-113">See [Querying Data](querying/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="f7931-114">データの保存</span><span class="sxs-lookup"><span data-stu-id="f7931-114">Saving Data</span></span>

<span data-ttu-id="f7931-115">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="f7931-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="f7931-116">詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7931-116">See [Saving Data](saving/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="f7931-117">次のステップ</span><span class="sxs-lookup"><span data-stu-id="f7931-117">Next steps</span></span>

<span data-ttu-id="f7931-118">入門チュートリアルについては、「[Entity Framework Core の概要](get-started/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7931-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>
