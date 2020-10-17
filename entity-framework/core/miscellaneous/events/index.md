---
title: ログとインターセプトの概要 - EF Core
description: EF Core のログ、イベント、インターセプター、診断の概要
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066496"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="5b286-103">ログとインターセプトの概要</span><span class="sxs-lookup"><span data-stu-id="5b286-103">Overview of logging and interception</span></span>

<span data-ttu-id="5b286-104">Entity Framework Core (EF Core) には、ログの生成、イベントへの応答、および診断の取得を行うためのメカニズムがいくつか含まれています。</span><span class="sxs-lookup"><span data-stu-id="5b286-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="5b286-105">これらはそれぞれ異なる状況に合わせて調整されており、複数のメカニズムが動作する可能性がある場合でも、タスクに最適なメカニズムを選択することが重要です。</span><span class="sxs-lookup"><span data-stu-id="5b286-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="5b286-106">たとえば、データベース インターセプターを使用して SQL をログに記録することもできますが、これはログ固有のメカニズムのいずれかの方がより適切に処理されます。</span><span class="sxs-lookup"><span data-stu-id="5b286-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="5b286-107">このページでは、これらの各メカニズムの概要を示し、それぞれを使用する状況について説明します。</span><span class="sxs-lookup"><span data-stu-id="5b286-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="5b286-108">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="5b286-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="5b286-109">この機能は、EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="5b286-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="5b286-110">[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) を使用することで、任意の種類のアプリケーションから EF Core のログにアクセスすることができます</span><span class="sxs-lookup"><span data-stu-id="5b286-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="5b286-111">([DbContext インスタンスの構成時](xref:core/miscellaneous/configuring-dbcontext))。</span><span class="sxs-lookup"><span data-stu-id="5b286-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="5b286-112">この構成は、通常、<xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> のオーバーライドで行われます。</span><span class="sxs-lookup"><span data-stu-id="5b286-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5b286-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5b286-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="5b286-114">この概念は、EF6 の <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> に似ています。</span><span class="sxs-lookup"><span data-stu-id="5b286-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="5b286-115">詳細については、「[シンプルなログ](xref:core/miscellaneous/events/simple-logging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5b286-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
