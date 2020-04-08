---
title: サポートされている .NET 実装 - EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 693d4cae85eddf86d01e17084415147c52a008c7
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413067"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="01294-102">EF Core でサポートされている .NET 実装</span><span class="sxs-lookup"><span data-stu-id="01294-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="01294-103">私達は、最新の .NET 実装を使用するすべての開発者が EF Core を使用できることを希望しており、その目標に向けて取り組んでいます。</span><span class="sxs-lookup"><span data-stu-id="01294-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="01294-104">EF Core の .NET Core でのサポートは、自動テストされており、多くのアプリケーションで正常に使用できることがわかっていますが、Mono、Xamarin、UWP ではいくつか問題があります。</span><span class="sxs-lookup"><span data-stu-id="01294-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="01294-105">概要</span><span class="sxs-lookup"><span data-stu-id="01294-105">Overview</span></span>

<span data-ttu-id="01294-106">.NET 実装のガイダンスを下の表にまとめました。</span><span class="sxs-lookup"><span data-stu-id="01294-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="01294-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="01294-107">EF Core</span></span>                       | <span data-ttu-id="01294-108">2.1 および 3.1</span><span class="sxs-lookup"><span data-stu-id="01294-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="01294-109">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="01294-109">.NET Standard</span></span>                 | <span data-ttu-id="01294-110">2.0</span><span class="sxs-lookup"><span data-stu-id="01294-110">2.0</span></span>         |
| <span data-ttu-id="01294-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="01294-111">.NET Core</span></span>                     | <span data-ttu-id="01294-112">2.0</span><span class="sxs-lookup"><span data-stu-id="01294-112">2.0</span></span>         |
| <span data-ttu-id="01294-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="01294-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="01294-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="01294-114">4.7.2</span></span>       |
| <span data-ttu-id="01294-115">Mono</span><span class="sxs-lookup"><span data-stu-id="01294-115">Mono</span></span>                          | <span data-ttu-id="01294-116">5.4</span><span class="sxs-lookup"><span data-stu-id="01294-116">5.4</span></span>         |
| <span data-ttu-id="01294-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="01294-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="01294-118">10.14</span><span class="sxs-lookup"><span data-stu-id="01294-118">10.14</span></span>       |
| <span data-ttu-id="01294-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="01294-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="01294-120">8.0</span><span class="sxs-lookup"><span data-stu-id="01294-120">8.0</span></span>         |
| <span data-ttu-id="01294-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="01294-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="01294-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="01294-122">10.0.16299</span></span>  |
| <span data-ttu-id="01294-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="01294-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="01294-124">2018.1</span><span class="sxs-lookup"><span data-stu-id="01294-124">2018.1</span></span>      |

<span data-ttu-id="01294-125"><sup>(1)</sup> 下記の「[.NET Framework](#net-framework)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01294-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="01294-126"><sup>(2)</sup> Xamarin には、EF Core 2 で開発された一部のアプリケーションが正しく動作しない問題と既知の制限があります。</span><span class="sxs-lookup"><span data-stu-id="01294-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="01294-127">回避策については、[アクティブな懸案事項](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)の一覧を確認してください。</span><span class="sxs-lookup"><span data-stu-id="01294-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="01294-128"><sup>(3)</sup> EF Core 2.0.1 以降をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01294-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="01294-129">[.NET Core UWP 6.x パッケージ](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="01294-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="01294-130">この記事の「[ユニバーサル Windows プラットフォーム](#universal-windows-platform)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="01294-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="01294-131"><sup>(4)</sup> Unity には、問題と既知の制限があります。</span><span class="sxs-lookup"><span data-stu-id="01294-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="01294-132">[アクティブな懸案事項](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)の一覧を確認してください。</span><span class="sxs-lookup"><span data-stu-id="01294-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="01294-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="01294-133">.NET Framework</span></span>

<span data-ttu-id="01294-134">.NET Framework をターゲットとするアプリケーションを .NET Standard のライブラリと連携させるには、次のような変更が必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="01294-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="01294-135">プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="01294-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="01294-136">テスト プロジェクトの場合、次のエントリも必要です。</span><span class="sxs-lookup"><span data-stu-id="01294-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="01294-137">それよりも古いバージョンの Visual Studio を使用する場合は、必ず [NuGet クライアントをバージョン 3.6.0 にアップグレードしてください](https://www.nuget.org/downloads)。これで .NET Standard 2.0 のライブラリと連携できるようになります。</span><span class="sxs-lookup"><span data-stu-id="01294-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="01294-138">また、可能であれば、NuGet の packages.config を PackageReference に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="01294-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="01294-139">次のプロパティをプロジェクト ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="01294-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="01294-140">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="01294-140">Universal Windows Platform</span></span>

<span data-ttu-id="01294-141">EF Core と .NET UWP の以前のバージョンには、多数の互換性の問題があり、特に .NET ネイティブ ツールチェーンでコンパイルされたアプリケーションの場合に顕著でした。</span><span class="sxs-lookup"><span data-stu-id="01294-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="01294-142">新しい .NET UWP バージョンでは、.NET Standard 2.0 のサポートが追加され、.NET Native 2.0 が含まれています。これにより、以前に報告されているほとんどの互換性の問題が解決されています。</span><span class="sxs-lookup"><span data-stu-id="01294-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="01294-143">EF Core 2.0.1 は、UWP でより完全にテストされていますが、テストは自動化されていません。</span><span class="sxs-lookup"><span data-stu-id="01294-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="01294-144">UWP で EF Core を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="01294-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="01294-145">クエリのパフォーマンスを最適化するには、LINQ クエリで匿名型を回避します。</span><span class="sxs-lookup"><span data-stu-id="01294-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="01294-146">UWP アプリケーションをアプリ ストアに配置するには、アプリケーションを .NET ネイティブでコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01294-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="01294-147">匿名型のクエリは、.NET ネイティブではパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="01294-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="01294-148">`SaveChanges()` のパフォーマンスを最適化するには、[ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) を使用し、エンティティ型で [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx)、[INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) を実装します。</span><span class="sxs-lookup"><span data-stu-id="01294-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="01294-149">問題のレポート</span><span class="sxs-lookup"><span data-stu-id="01294-149">Report issues</span></span>

<span data-ttu-id="01294-150">予想どおりに機能しない組み合わせについては、[EF Core イシュー追跡ツール](https://github.com/aspnet/entityframeworkcore/issues/new)で新しいイシューを登録することが推奨されています。</span><span class="sxs-lookup"><span data-stu-id="01294-150">For any combination that doesn�t work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="01294-151">Xamarin 関連の問題については、 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) または [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new) の問題追跡ツールをご利用ください。</span><span class="sxs-lookup"><span data-stu-id="01294-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
