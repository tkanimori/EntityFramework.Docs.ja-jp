---
title: ログとインターセプトの概要 - EF Core
description: EF Core のログ、イベント、インターセプター、診断の概要
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 2c44772b22112645f85cf0bffa680bc510ea5afb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003524"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="f306e-103">ログとインターセプトの概要</span><span class="sxs-lookup"><span data-stu-id="f306e-103">Overview of Logging and Interception</span></span>

<span data-ttu-id="f306e-104">Entity Framework Core (EF Core) には、ログの生成、イベントへの応答、および診断の取得を行うためのメカニズムがいくつか含まれています。</span><span class="sxs-lookup"><span data-stu-id="f306e-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="f306e-105">これらはそれぞれ異なる状況に合わせて調整されており、複数のメカニズムが動作する可能性がある場合でも、タスクに最適なメカニズムを選択することが重要です。</span><span class="sxs-lookup"><span data-stu-id="f306e-105">Each of these is tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="f306e-106">たとえば、データベース インターセプターを使用して SQL をログに記録することもできますが、これはログに合わせて調整されたメカニズムの方がより適切に処理されます。</span><span class="sxs-lookup"><span data-stu-id="f306e-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the mechanisms tailored to logging.</span></span> <span data-ttu-id="f306e-107">このページでは、これらの各メカニズムの概要を示し、それぞれを使用する状況について説明します。</span><span class="sxs-lookup"><span data-stu-id="f306e-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="quick-reference"></a><span data-ttu-id="f306e-108">クイック リファレンス</span><span class="sxs-lookup"><span data-stu-id="f306e-108">Quick reference</span></span>

<span data-ttu-id="f306e-109">以下の表に、ここで説明するメカニズムの違いに関するクイック リファレンスを示します。</span><span class="sxs-lookup"><span data-stu-id="f306e-109">The table below provides a quick reference for the differences between the mechanisms described here.</span></span>

| <span data-ttu-id="f306e-110">メカニズム</span><span class="sxs-lookup"><span data-stu-id="f306e-110">Mechanism</span></span> |  <span data-ttu-id="f306e-111">Async</span><span class="sxs-lookup"><span data-stu-id="f306e-111">Async</span></span> | <span data-ttu-id="f306e-112">Scope</span><span class="sxs-lookup"><span data-stu-id="f306e-112">Scope</span></span> | <span data-ttu-id="f306e-113">登録済み</span><span class="sxs-lookup"><span data-stu-id="f306e-113">Registered</span></span> | <span data-ttu-id="f306e-114">使用目的</span><span class="sxs-lookup"><span data-stu-id="f306e-114">Intended use</span></span>
|:----------|--------|-------|------------|-------------
| <span data-ttu-id="f306e-115">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="f306e-115">Simple Logging</span></span> | <span data-ttu-id="f306e-116">いいえ</span><span class="sxs-lookup"><span data-stu-id="f306e-116">No</span></span> | <span data-ttu-id="f306e-117">コンテキストごと</span><span class="sxs-lookup"><span data-stu-id="f306e-117">Per context</span></span> | <span data-ttu-id="f306e-118">コンテキスト構成</span><span class="sxs-lookup"><span data-stu-id="f306e-118">Context configuration</span></span> | <span data-ttu-id="f306e-119">開発時のログ</span><span class="sxs-lookup"><span data-stu-id="f306e-119">Development-time logging</span></span>
| <span data-ttu-id="f306e-120">Microsoft.Extensions.Logging</span><span class="sxs-lookup"><span data-stu-id="f306e-120">Microsoft.Extensions.Logging</span></span> | <span data-ttu-id="f306e-121">いいえ</span><span class="sxs-lookup"><span data-stu-id="f306e-121">No</span></span> | <span data-ttu-id="f306e-122">コンテキストごと\*</span><span class="sxs-lookup"><span data-stu-id="f306e-122">Per context\*</span></span> | <span data-ttu-id="f306e-123">D.I.</span><span class="sxs-lookup"><span data-stu-id="f306e-123">D.I.</span></span> <span data-ttu-id="f306e-124">またはコンテキスト構成</span><span class="sxs-lookup"><span data-stu-id="f306e-124">or context configuration</span></span> | <span data-ttu-id="f306e-125">本番環境のログ</span><span class="sxs-lookup"><span data-stu-id="f306e-125">Production logging</span></span>
| <span data-ttu-id="f306e-126">イベント</span><span class="sxs-lookup"><span data-stu-id="f306e-126">Events</span></span> | <span data-ttu-id="f306e-127">いいえ</span><span class="sxs-lookup"><span data-stu-id="f306e-127">No</span></span> | <span data-ttu-id="f306e-128">コンテキストごと</span><span class="sxs-lookup"><span data-stu-id="f306e-128">Per context</span></span> | <span data-ttu-id="f306e-129">時間指定なし</span><span class="sxs-lookup"><span data-stu-id="f306e-129">Any time</span></span> | <span data-ttu-id="f306e-130">EF イベントへの応答</span><span class="sxs-lookup"><span data-stu-id="f306e-130">Reacting to EF events</span></span>
| <span data-ttu-id="f306e-131">インターセプター</span><span class="sxs-lookup"><span data-stu-id="f306e-131">Interceptors</span></span> | <span data-ttu-id="f306e-132">Yes</span><span class="sxs-lookup"><span data-stu-id="f306e-132">Yes</span></span> | <span data-ttu-id="f306e-133">コンテキストごと</span><span class="sxs-lookup"><span data-stu-id="f306e-133">Per context</span></span> | <span data-ttu-id="f306e-134">コンテキスト構成</span><span class="sxs-lookup"><span data-stu-id="f306e-134">Context configuration</span></span> | <span data-ttu-id="f306e-135">EF 操作の取り扱い</span><span class="sxs-lookup"><span data-stu-id="f306e-135">Manipulating EF operations</span></span>
| <span data-ttu-id="f306e-136">診断リスナー</span><span class="sxs-lookup"><span data-stu-id="f306e-136">Diagnostics listeners</span></span> | <span data-ttu-id="f306e-137">いいえ</span><span class="sxs-lookup"><span data-stu-id="f306e-137">No</span></span> | <span data-ttu-id="f306e-138">プロセス</span><span class="sxs-lookup"><span data-stu-id="f306e-138">Process</span></span> | <span data-ttu-id="f306e-139">グローバル</span><span class="sxs-lookup"><span data-stu-id="f306e-139">Globally</span></span> | <span data-ttu-id="f306e-140">アプリケーション診断</span><span class="sxs-lookup"><span data-stu-id="f306e-140">Application diagnostics</span></span>

<span data-ttu-id="f306e-141">\*通常、`Microsoft.Extensions.Logging` は依存関係の挿入を使用してアプリケーションごとに構成されますが、EF レベルでは、必要に応じて異なるロガーを使用して各コンテキストを構成 _できます_。</span><span class="sxs-lookup"><span data-stu-id="f306e-141">\*Typically `Microsoft.Extensions.Logging` is configured per-application via dependency injection However, at the EF level, each context _can_ be configured with a different logger if needed.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="f306e-142">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="f306e-142">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="f306e-143">この機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f306e-143">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="f306e-144">[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) を使用すると、任意の種類のアプリケーションから EF Core ログにアクセスできます</span><span class="sxs-lookup"><span data-stu-id="f306e-144">EF Core logs can be accessed from any type of application through the use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="f306e-145">([DbContext インスタンスの構成時](xref:core/dbcontext-configuration/index))。</span><span class="sxs-lookup"><span data-stu-id="f306e-145">when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="f306e-146">この構成は、通常、<xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> のオーバーライドで行われます。</span><span class="sxs-lookup"><span data-stu-id="f306e-146">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f306e-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f306e-147">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="f306e-148">この概念は、EF6 の <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> に似ています。</span><span class="sxs-lookup"><span data-stu-id="f306e-148">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="f306e-149">詳細については、「[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f306e-149">See [Simple Logging](xref:core/logging-events-diagnostics/simple-logging) for more information.</span></span>

## <a name="microsoftextensionslogging"></a><span data-ttu-id="f306e-150">Microsoft.Extensions.Logging</span><span class="sxs-lookup"><span data-stu-id="f306e-150">Microsoft.Extensions.Logging</span></span>

<span data-ttu-id="f306e-151">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) は、多くの一般的なログ システム用のプラグイン プロバイダーを備えた拡張可能なログ メカニズムです。</span><span class="sxs-lookup"><span data-stu-id="f306e-151">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="f306e-152">EF Core は `Microsoft.Extensions.Logging` と完全に統合されており、ASP.NET Core アプリケーションでは既定でこの形式のログが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f306e-152">EF Core fully integrates with `Microsoft.Extensions.Logging` and this form of logging is used by default for ASP.NET Core applications.</span></span>

<span data-ttu-id="f306e-153">詳細については、[EF Core での Microsoft.Extensions.Logging の使用](xref:core/logging-events-diagnostics/extensions-logging)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f306e-153">See [Using Microsoft.Extensions.Logging in EF Core](xref:core/logging-events-diagnostics/extensions-logging) for more information.</span></span>

## <a name="events"></a><span data-ttu-id="f306e-154">イベント</span><span class="sxs-lookup"><span data-stu-id="f306e-154">Events</span></span>

> [!NOTE]
> <span data-ttu-id="f306e-155">EF Core 5.0 では、追加のイベントが導入されました。</span><span class="sxs-lookup"><span data-stu-id="f306e-155">Additional events were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="f306e-156">EF Core では、EF Core コードで特定の処理が発生したときにコールバックとして機能する [.NET イベント](/dotnet/standard/events/)が公開されます。</span><span class="sxs-lookup"><span data-stu-id="f306e-156">EF Core exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="f306e-157">イベントはインターセプターよりシンプルであり、より柔軟な登録が可能です。</span><span class="sxs-lookup"><span data-stu-id="f306e-157">Events are simpler than interceptors and allow more flexible registration.</span></span> <span data-ttu-id="f306e-158">ただし、これらは同期のみであるため、非ブロッキングの非同期 I/O を実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="f306e-158">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="f306e-159">イベントは DbContext インスタンスごとに登録され、この登録はいつでも行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f306e-159">Events are registered per DbContext instance and this registration can be done at any time.</span></span> <span data-ttu-id="f306e-160">プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f306e-160">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

<span data-ttu-id="f306e-161">詳細については、「[EF Core での .NET イベント](xref:core/logging-events-diagnostics/events)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f306e-161">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

## <a name="interception"></a><span data-ttu-id="f306e-162">interception</span><span class="sxs-lookup"><span data-stu-id="f306e-162">Interception</span></span>

> [!NOTE]
> <span data-ttu-id="f306e-163">この機能は EF Core 3.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f306e-163">This feature was introduced in EF Core 3.0.</span></span> <span data-ttu-id="f306e-164">EF Core 5.0 では、追加のインターセプターが導入されました。</span><span class="sxs-lookup"><span data-stu-id="f306e-164">Additional interceptors were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="f306e-165">EF Core インターセプターを使用すると、EF Core 操作をインターセプト、変更、抑制できます。</span><span class="sxs-lookup"><span data-stu-id="f306e-165">EF Core interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="f306e-166">これには、コマンド実行などの低レベルのデータベース操作と SaveChanges の呼び出しなどの上位レベルの操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f306e-166">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="f306e-167">インターセプターは、インターセプトしている操作を変更または抑制できる点がログや診断と異なります。</span><span class="sxs-lookup"><span data-stu-id="f306e-167">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="f306e-168">ログ記録には、[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)または [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) の使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f306e-168">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="f306e-169">インターセプターは DbContext インスタンスごとに登録され、この登録はいつでも行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f306e-169">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="f306e-170">プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f306e-170">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

<span data-ttu-id="f306e-171">詳細については、[インターセプト](xref:core/logging-events-diagnostics/interceptors)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f306e-171">See [Interception](xref:core/logging-events-diagnostics/interceptors) for more information.</span></span>

## <a name="diagnostic-listeners"></a><span data-ttu-id="f306e-172">診断リスナー</span><span class="sxs-lookup"><span data-stu-id="f306e-172">Diagnostic listeners</span></span>

<span data-ttu-id="f306e-173">診断リスナーを使用すると、現在の .NET プロセスで発生する任意の EF Core イベントをリッスンできます。</span><span class="sxs-lookup"><span data-stu-id="f306e-173">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span>

<span data-ttu-id="f306e-174">診断リスナーは、単一の DbContext インスタンスからイベントを取得するのには適していません。</span><span class="sxs-lookup"><span data-stu-id="f306e-174">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="f306e-175">EF Core インターセプターは、コンテキストごとの登録によって同じイベントへのアクセスを可能にします。</span><span class="sxs-lookup"><span data-stu-id="f306e-175">EF Core interceptors provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="f306e-176">診断リスナーは、ログ記録向けに設計されていません。</span><span class="sxs-lookup"><span data-stu-id="f306e-176">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="f306e-177">ログ記録には、[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)または [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) の使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f306e-177">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="f306e-178">詳細については、「[EF Core での診断リスナーの使用](xref:core/logging-events-diagnostics/diagnostic-listeners)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f306e-178">See [Using diagnostic listeners in EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners) for more information.</span></span>
