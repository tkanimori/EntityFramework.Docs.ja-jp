---
title: Entity Framework Core の概要 - EF Core
description: Entity Framework Core の一般的な概要
author: rowanmiller
ms.date: 10/27/2016
uid: core/index
ms.openlocfilehash: 1d320ecae06d9c05fe3a14ec955f7151de6a56e5
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071928"
---
# <a name="entity-framework-core"></a><span data-ttu-id="77b88-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="77b88-103">Entity Framework Core</span></span>

<span data-ttu-id="77b88-104">Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="77b88-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="77b88-105">EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。</span><span class="sxs-lookup"><span data-stu-id="77b88-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="77b88-106">EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](xref:core/providers/index)」(データベース プロバイダー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77b88-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="77b88-107">モデル</span><span class="sxs-lookup"><span data-stu-id="77b88-107">The Model</span></span>

<span data-ttu-id="77b88-108">EF Core では、データ アクセスはモデルを利用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="77b88-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="77b88-109">モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="77b88-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="77b88-110">詳細については、「[Creating a Model](xref:core/modeling/index)」 (モデルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77b88-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="77b88-111">既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、[EF 移行](xref:core/managing-schemas/migrations/index)を使ってモデルからデータベースを作成してから、モデルが時間の経過と共に変化するのに合わせてそれを進化させたりできます。</span><span class="sxs-lookup"><span data-stu-id="77b88-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="77b88-112">クエリ実行</span><span class="sxs-lookup"><span data-stu-id="77b88-112">Querying</span></span>

<span data-ttu-id="77b88-113">エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="77b88-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="77b88-114">詳細については、「[Querying Data](xref:core/querying/index)」 (データのクエリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77b88-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="77b88-115">データの保存</span><span class="sxs-lookup"><span data-stu-id="77b88-115">Saving Data</span></span>

<span data-ttu-id="77b88-116">データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。</span><span class="sxs-lookup"><span data-stu-id="77b88-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="77b88-117">詳細については、「[Saving Data](xref:core/saving/index)」 (データの保存) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77b88-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="77b88-118">次の手順</span><span class="sxs-lookup"><span data-stu-id="77b88-118">Next steps</span></span>

<span data-ttu-id="77b88-119">入門チュートリアルについては、「[Entity Framework Core の概要](xref:core/get-started/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77b88-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
