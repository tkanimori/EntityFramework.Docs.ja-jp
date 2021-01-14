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
# <a name="related-data-and-serialization"></a><span data-ttu-id="6aaab-103">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="6aaab-103">Related data and serialization</span></span>

<span data-ttu-id="6aaab-104">EF Core ではナビゲーション プロパティの修復が自動的に行われるので、最終的にオブジェクト グラフの循環が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6aaab-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="6aaab-105">たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="6aaab-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="6aaab-106">これらの各投稿には元のブログへの参照が含まれることになります。</span><span class="sxs-lookup"><span data-stu-id="6aaab-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="6aaab-107">一部のシリアル化フレームワークでは、このような循環は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="6aaab-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="6aaab-108">たとえば、Json.NET では、循環が発生した場合に次の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6aaab-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="6aaab-109">Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="6aaab-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="6aaab-110">ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="6aaab-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="6aaab-111">この構成は、`Startup.cs` の `ConfigureServices(...)` メソッドで行われます。</span><span class="sxs-lookup"><span data-stu-id="6aaab-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="6aaab-112">別の方法は、いずれかのナビゲーション プロパティを `[JsonIgnore]` 属性で装飾し、シリアル化中にそのナビゲーション プロパティを走査しないように Json.NET に指示することです。</span><span class="sxs-lookup"><span data-stu-id="6aaab-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
