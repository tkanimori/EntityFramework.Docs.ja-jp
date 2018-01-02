---
title: "Entity Framework でコンポーネントをテストする - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/index
ms.openlocfilehash: c82c25da393c39cf5e2deb46c7322e7395051937
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="testing"></a><span data-ttu-id="80795-102">テスト中</span><span class="sxs-lookup"><span data-stu-id="80795-102">Testing</span></span>

<span data-ttu-id="80795-103">本物のデータベースへの接続をシミュレーションすることでコンポーネントをテストすることがあります。実際にデータベース I/O を操作してオーバーヘッドを発生させることを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="80795-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="80795-104">これには、次の 2 つの選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="80795-104">There are two main options for doing this:</span></span>
 * <span data-ttu-id="80795-105">[SQLite メモリ内モード](sqlite.md)では、リレーショナル データベースのように振る舞うプロバイダーに対して効率的なテストを記述できます。</span><span class="sxs-lookup"><span data-stu-id="80795-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
 * <span data-ttu-id="80795-106">[InMemory プロバイダー](in-memory.md)は依存性が最小限に抑えられた軽量のプロバイダーですが、常にリレーショナル データベースのように振る舞うとは限りません。</span><span class="sxs-lookup"><span data-stu-id="80795-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
