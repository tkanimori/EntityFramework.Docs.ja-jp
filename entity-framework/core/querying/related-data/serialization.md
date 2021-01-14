---
title: 関連データとシリアル化 - EF Core
description: Entity Framework Core による関連データのサイクルがシリアル化フレームワークに与える影響に関する情報
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 3e1152f09493d039032d9d88493b52a5ef425eb9
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129136"
---
# <a name="related-data-and-serialization"></a>関連データとシリアル化

EF Core ではナビゲーション プロパティの修復が自動的に行われるので、最終的にオブジェクト グラフの循環が生じる可能性があります。 たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。 これらの各投稿には元のブログへの参照が含まれることになります。

一部のシリアル化フレームワークでは、このような循環は許可されていません。 たとえば、Json.NET では、循環が発生した場合に次の例外がスローされます。

> Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.

ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。 この構成は、`Startup.cs` の `ConfigureServices(...)` メソッドで行われます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

別の方法は、いずれかのナビゲーション プロパティを `[JsonIgnore]` 属性で装飾し、シリアル化中にそのナビゲーション プロパティを走査しないように Json.NET に指示することです。
