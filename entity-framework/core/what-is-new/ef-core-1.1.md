---
title: EF Core 1.1 の新機能 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: d582712ed62443318f4b9e209511fb2a557d667e
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656011"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="640e8-102">EF Core 1.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="640e8-102">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="640e8-103">モデリング</span><span class="sxs-lookup"><span data-stu-id="640e8-103">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="640e8-104">フィールド マッピング</span><span class="sxs-lookup"><span data-stu-id="640e8-104">Field mapping</span></span>

<span data-ttu-id="640e8-105">プロパティのバッキング フィールドを構成できます。</span><span class="sxs-lookup"><span data-stu-id="640e8-105">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="640e8-106">これは読み取り専用プロパティや、プロパティではなく Get/Set メソッドを持つデータで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="640e8-106">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="640e8-107">SQL Server でのメモリ最適化テーブルへのマッピング</span><span class="sxs-lookup"><span data-stu-id="640e8-107">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="640e8-108">エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="640e8-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="640e8-109">EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="640e8-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="640e8-110">Change tracking</span><span class="sxs-lookup"><span data-stu-id="640e8-110">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="640e8-111">EF6 から追加された変更追跡 API</span><span class="sxs-lookup"><span data-stu-id="640e8-111">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="640e8-112">`Reload`、`GetModifiedProperties`、`GetDatabaseValues` などです。</span><span class="sxs-lookup"><span data-stu-id="640e8-112">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="640e8-113">クエリ</span><span class="sxs-lookup"><span data-stu-id="640e8-113">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="640e8-114">明示的な読み込み</span><span class="sxs-lookup"><span data-stu-id="640e8-114">Explicit Loading</span></span>

<span data-ttu-id="640e8-115">以前にデータベースから読み込まれたエンティティのナビゲーション プロパティの作成をトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="640e8-115">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="640e8-116">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="640e8-116">DbSet.Find</span></span>

<span data-ttu-id="640e8-117">主キーの値に基づいてエンティティをフェッチする簡単な方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="640e8-117">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="640e8-118">その他</span><span class="sxs-lookup"><span data-stu-id="640e8-118">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="640e8-119">接続の復元性</span><span class="sxs-lookup"><span data-stu-id="640e8-119">Connection resiliency</span></span>

<span data-ttu-id="640e8-120">失敗したデータベース コマンドを自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="640e8-120">Automatically retries failed database commands.</span></span> <span data-ttu-id="640e8-121">これは、一時的なエラーが発生しやすい SQL Azure への接続で特に有用です。</span><span class="sxs-lookup"><span data-stu-id="640e8-121">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="640e8-122">サービスの置き換えの簡略化</span><span class="sxs-lookup"><span data-stu-id="640e8-122">Simplified service replacement</span></span>

<span data-ttu-id="640e8-123">EF で使用される内部サービスの置き換えが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="640e8-123">Makes it easier to replace internal services that EF uses.</span></span>
