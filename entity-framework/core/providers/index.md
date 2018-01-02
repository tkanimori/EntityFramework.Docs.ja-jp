---
title: "データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/providers/index
ms.openlocfilehash: 19c275b7e89c62e79c8bded977e39b2cfb2b439a
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="database-providers"></a><span data-ttu-id="603f6-102">データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="603f6-102">Database Providers</span></span>

<span data-ttu-id="603f6-103">Entity Framework Core ではプロバイダー モデルを使用することで、さまざまなデータベースへのアクセスに EF を利用できます。</span><span class="sxs-lookup"><span data-stu-id="603f6-103">Entity Framework Core uses a provider model to allow EF to be used to access many different databases.</span></span> <span data-ttu-id="603f6-104">いくつかの概念はほとんどのデータベースに共通しており、プライマリ EF Core コンポーネントに含まれています。</span><span class="sxs-lookup"><span data-stu-id="603f6-104">Some concepts are common to most databases, and are included in the primary EF Core components.</span></span> <span data-ttu-id="603f6-105">そのような概念には、LINQ のクエリの表現、トランザクション、データベースから読み込んだときのオブジェクトの変更追跡などがあります。</span><span class="sxs-lookup"><span data-stu-id="603f6-105">Such concepts include expressing queries in LINQ, transactions, and tacking changes to objects once they are loaded from the database.</span></span> <span data-ttu-id="603f6-106">いくつかの概念は、特定のプロバイダーに固有のものです。</span><span class="sxs-lookup"><span data-stu-id="603f6-106">Some concepts are specific to a particular provider.</span></span> <span data-ttu-id="603f6-107">たとえば、SQL Server プロバイダーの場合、メモリが最適化されたテーブル (SQL Server 固有の機能) を構成できます。</span><span class="sxs-lookup"><span data-stu-id="603f6-107">For example, the SQL Server provider allows you to configure memory-optimized tables (a feature specific to SQL Server).</span></span> <span data-ttu-id="603f6-108">その他の概念は、プロバイダーのクラスに固有のものです。</span><span class="sxs-lookup"><span data-stu-id="603f6-108">Other concepts are specific to a class of providers.</span></span> <span data-ttu-id="603f6-109">たとえば、リレーショナル データベースの EF Core プロバイダーの場合、一般的な `Microsoft.EntityFrameworkCore.Relational` ライブラリに基づいてビルドします。このライブラリは、テーブルと列のマッピングや外部キー制約などを構成するための API を提供します。</span><span class="sxs-lookup"><span data-stu-id="603f6-109">For example, EF Core providers for relational databases build on the common `Microsoft.EntityFrameworkCore.Relational` library, which provides APIs for configuring table and column mappings, foreign key constraints, etc.</span></span>

<span data-ttu-id="603f6-110">EF Core プロバイダーは、さまざまなソースによってビルドされます。</span><span class="sxs-lookup"><span data-stu-id="603f6-110">EF Core providers are built by a variety of sources.</span></span> <span data-ttu-id="603f6-111">一部のプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="603f6-111">Not all providers are maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="603f6-112">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="603f6-112">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>
