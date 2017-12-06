---
title: "デザイン時のサービスの EF コア"
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
ms.technology: entity-framework-core
ms.openlocfilehash: f9c8208a59bfcefeaab01ea69e65fe809a0b3d89
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="design-time-services"></a>デザイン時のサービス
====================
ツールによって使用されるいくつかのサービスは、デザイン時にのみ使用されます。 これらのサービスは、アプリを配置するように EF コア ランタイム サービスとは別に管理されます。 これらのサービス (たとえば移行ファイルを生成するサービス) のいずれかを上書きするには、実装を追加`IDesignTimeServices`をスタートアップ プロジェクトにします。

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
