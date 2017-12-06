---
title: "ツールと拡張機能 - Detached.EntityFramework EF コア"
author: ErikEJ
ms.author: divega
ms.date: 01/19/2017
ms.assetid: DE1C3FEA-F618-4C11-932F-7C392A1B2C94
ms.technology: entity-framework-core
uid: core/extensions/entityframeworkcore-detached
ms.openlocfilehash: 93937eee07a9e1a0b94bd92140faec7d1753332f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="efdetachedentityframework-extension"></a><span data-ttu-id="49209-102">EFDetached.EntityFramework 拡張機能</span><span class="sxs-lookup"><span data-stu-id="49209-102">EFDetached.EntityFramework Extension</span></span>

> [!NOTE]  
> <span data-ttu-id="49209-103">この拡張機能は、Entity Framework Core プロジェクトの一部としては維持されません。</span><span class="sxs-lookup"><span data-stu-id="49209-103">This extension is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="49209-104">サード パーティ製の拡張機能を考慮する場合は、品質、ライセンス、サポートなど、要件を満たしていることを確認するを評価することを確認します。</span><span class="sxs-lookup"><span data-stu-id="49209-104">When considering a third party extension, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

<span data-ttu-id="49209-105">ロードし、デタッチされたエンティティ全体のグラフ (エンティティをその子エンティティとリスト) を保存します。</span><span class="sxs-lookup"><span data-stu-id="49209-105">Loads and saves entire detached entity graphs (the entity with their child entities and lists).</span></span> <span data-ttu-id="49209-106">インスパイアされた[GraphDiff](https://github.com/refactorthis/GraphDiff/)です。</span><span class="sxs-lookup"><span data-stu-id="49209-106">Inspired by [GraphDiff](https://github.com/refactorthis/GraphDiff/).</span></span> <span data-ttu-id="49209-107">つまりも simplificate 監査と改ページなどのいくつかの反復的なタスクに一部のプラグインを追加します。</span><span class="sxs-lookup"><span data-stu-id="49209-107">The idea is also add some plugins to simplificate some repetitive tasks, like auditing and pagination.</span></span>

<span data-ttu-id="49209-108">次のリソースに役立つ Detached.EntityFramework の概要</span><span class="sxs-lookup"><span data-stu-id="49209-108">The following resource will help you get started with Detached.EntityFramework</span></span>
* [<span data-ttu-id="49209-109">Detached.EntityFramework GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="49209-109">Detached.EntityFramework GitHub repository</span></span>](https://github.com/leonardoporro/Detached/)
