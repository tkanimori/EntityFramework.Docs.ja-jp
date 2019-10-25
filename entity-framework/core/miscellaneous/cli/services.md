---
title: デザイン時サービス-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: ff0243a588d5e957aed89fcf1ce7462b5b9a747a
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811941"
---
# <a name="design-time-services"></a><span data-ttu-id="dbd26-102">デザイン時サービス</span><span class="sxs-lookup"><span data-stu-id="dbd26-102">Design-time services</span></span>

<span data-ttu-id="dbd26-103">ツールで使用される一部のサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="dbd26-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="dbd26-104">これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。</span><span class="sxs-lookup"><span data-stu-id="dbd26-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="dbd26-105">これらのサービスのいずれかをオーバーライドするには (たとえば、移行ファイルを生成するサービス)、スタートアッププロジェクトに `IDesignTimeServices` の実装を追加します。</span><span class="sxs-lookup"><span data-stu-id="dbd26-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
