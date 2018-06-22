---
title: デザイン時のサービスの EF コア
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.technology: entity-framework-core
ms.openlocfilehash: f9c8208a59bfcefeaab01ea69e65fe809a0b3d89
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
ms.locfileid: "26053692"
---
<a name="design-time-services"></a><span data-ttu-id="00dd9-102">デザイン時のサービス</span><span class="sxs-lookup"><span data-stu-id="00dd9-102">Design-time services</span></span>
====================
<span data-ttu-id="00dd9-103">ツールによって使用されるいくつかのサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="00dd9-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="00dd9-104">これらのサービスは、アプリを配置するように EF コア ランタイム サービスとは別に管理されます。</span><span class="sxs-lookup"><span data-stu-id="00dd9-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="00dd9-105">これらのサービス (たとえば移行ファイルを生成するサービス) のいずれかを上書きするには、実装を追加`IDesignTimeServices`をスタートアップ プロジェクトにします。</span><span class="sxs-lookup"><span data-stu-id="00dd9-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
