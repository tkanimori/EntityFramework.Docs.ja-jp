---
title: デザイン時サービス-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414207"
---
# <a name="design-time-services"></a><span data-ttu-id="387ba-102">デザイン時サービス</span><span class="sxs-lookup"><span data-stu-id="387ba-102">Design-time services</span></span>

<span data-ttu-id="387ba-103">ツールで使用される一部のサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="387ba-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="387ba-104">これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。</span><span class="sxs-lookup"><span data-stu-id="387ba-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="387ba-105">これらのサービスのいずれかをオーバーライドするには (たとえば、移行ファイルを生成するサービス)、スタートアッププロジェクトに `IDesignTimeServices` の実装を追加します。</span><span class="sxs-lookup"><span data-stu-id="387ba-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
