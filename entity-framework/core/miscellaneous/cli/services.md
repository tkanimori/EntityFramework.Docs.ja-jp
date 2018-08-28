---
title: デザイン時サービス - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.openlocfilehash: e1cacdd4f40f9c395d8c88a91df4a92ef27001a8
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997532"
---
<a name="design-time-services"></a><span data-ttu-id="2ce1e-102">デザイン時サービス</span><span class="sxs-lookup"><span data-stu-id="2ce1e-102">Design-time services</span></span>
====================
<span data-ttu-id="2ce1e-103">ツールによって使用されるいくつかのサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="2ce1e-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="2ce1e-104">これらのサービスは、アプリを使ってデプロイされるを防ぐために EF Core のランタイム サービスとは別に管理されます。</span><span class="sxs-lookup"><span data-stu-id="2ce1e-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="2ce1e-105">これらのサービス (たとえば、移行ファイルを生成するサービス) のいずれかを無効にするには、実装を追加`IDesignTimeServices`をスタートアップ プロジェクトにします。</span><span class="sxs-lookup"><span data-stu-id="2ce1e-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
