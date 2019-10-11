---
title: EF Core を使ったコンポーネントのテスト - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 1603be0c-69bc-4dd9-9a08-3d0129cdc6c1
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 8de7df80ce91c4d94133a96d759dd552d0ba1884
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181310"
---
# <a name="testing-components-using-ef-core"></a><span data-ttu-id="b248d-102">EF Core を使ったコンポーネントのテスト</span><span class="sxs-lookup"><span data-stu-id="b248d-102">Testing components using EF Core</span></span>

<span data-ttu-id="b248d-103">本物のデータベースへの接続をシミュレーションすることでコンポーネントをテストすることがあります。実際にデータベース I/O を操作してオーバーヘッドを発生させることを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="b248d-103">You may want to test components using something that approximates connecting to the real database, without the overhead of actual database I/O operations.</span></span>

<span data-ttu-id="b248d-104">これには、次の 2 つの選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="b248d-104">There are two main options for doing this:</span></span>
 * <span data-ttu-id="b248d-105">[SQLite メモリ内モード](sqlite.md)では、リレーショナル データベースのように振る舞うプロバイダーに対して効率的なテストを記述できます。</span><span class="sxs-lookup"><span data-stu-id="b248d-105">[SQLite in-memory mode](sqlite.md) allows you to write efficient tests against a provider that behaves like a relational database.</span></span>
 * <span data-ttu-id="b248d-106">[InMemory プロバイダー](in-memory.md)は依存性が最小限に抑えられた軽量のプロバイダーですが、常にリレーショナル データベースのように振る舞うとは限りません。</span><span class="sxs-lookup"><span data-stu-id="b248d-106">[The InMemory provider](in-memory.md) is a lightweight provider that has minimal dependencies, but does not always behave like a relational database.</span></span>
