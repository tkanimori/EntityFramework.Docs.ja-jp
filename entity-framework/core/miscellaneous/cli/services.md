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
<a name="design-time-services"></a>デザイン時サービス
====================
ツールによって使用されるいくつかのサービスは、デザイン時にのみ使用されます。 これらのサービスは、アプリを使ってデプロイされるを防ぐために EF Core のランタイム サービスとは別に管理されます。 これらのサービス (たとえば、移行ファイルを生成するサービス) のいずれかを無効にするには、実装を追加`IDesignTimeServices`をスタートアップ プロジェクトにします。

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```
