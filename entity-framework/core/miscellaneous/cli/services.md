---
title: デザイン時サービス-EF Core
description: Entity Framework Core デザイン時サービスに関する情報
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061945"
---
# <a name="design-time-services"></a><span data-ttu-id="9d937-103">デザイン時サービス</span><span class="sxs-lookup"><span data-stu-id="9d937-103">Design-time services</span></span>

<span data-ttu-id="9d937-104">ツールで使用される一部のサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d937-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="9d937-105">これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。</span><span class="sxs-lookup"><span data-stu-id="9d937-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="9d937-106">これらのサービスの1つ (たとえば、移行ファイルを生成するサービス) をオーバーライドするには、の実装を `IDesignTimeServices` スタートアッププロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="9d937-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
