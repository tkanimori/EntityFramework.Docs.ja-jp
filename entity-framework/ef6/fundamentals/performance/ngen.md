---
title: アセンブリを NGen - EF6 で起動時のパフォーマンスを向上させる
author: divega
ms.date: 2016-10-23
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
ms.openlocfilehash: 324769ca6ee95e1576cf03d20e569f8b24778119
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998336"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="19c0d-102">アセンブリを NGen で起動時のパフォーマンスを向上させる</span><span class="sxs-lookup"><span data-stu-id="19c0d-102">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="19c0d-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="19c0d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="19c0d-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="19c0d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="19c0d-105">アプリケーションを迅速に起動する方法として、または場合によってはでライブラリを使用して、メモリが少ないを .NET Framework は、マネージ アプリケーションのネイティブ イメージの生成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="19c0d-105">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="19c0d-106">ネイティブ イメージが軽減は、.NET JIT (ジャスト イン タイム) コンパイラでネイティブ命令を生成することをマネージ コード アセンブリをアプリケーションが実行される前に、ネイティブ機械語命令を含むファイルに変換することによって作成されます。アプリケーション ランタイム。</span><span class="sxs-lookup"><span data-stu-id="19c0d-106">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="19c0d-107">前のバージョン 6、EF のランタイムの core ライブラリ、.NET Framework の一部であったし、それらのネイティブ イメージが自動的に生成されました。</span><span class="sxs-lookup"><span data-stu-id="19c0d-107">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="19c0d-108">バージョン 6 以降で、全体の EF ランタイムは、EntityFramework NuGet パッケージに統合されています。</span><span class="sxs-lookup"><span data-stu-id="19c0d-108">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="19c0d-109">ネイティブ イメージが今すぐに生成、NGen.exe コマンド ライン ツールを使用して同様の結果を取得します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-109">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="19c0d-110">経験の観測値では、EF のランタイム アセンブリのネイティブ イメージは、アプリケーションの起動時間の 1 ~ 3 秒の間で減らすことができますを表示します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-110">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="19c0d-111">NGen.exe を使用する方法</span><span class="sxs-lookup"><span data-stu-id="19c0d-111">How to use NGen.exe</span></span>  

<span data-ttu-id="19c0d-112">NGen.exe ツールの最も基本的な機能は、「インストール」する (つまりを作成し、ディスクに永続化) アセンブリとその直接の依存関係のネイティブ イメージ。</span><span class="sxs-lookup"><span data-stu-id="19c0d-112">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="19c0d-113">次を実現方法を示します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-113">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="19c0d-114">管理者としてコマンド プロンプト ウィンドウを開きます</span><span class="sxs-lookup"><span data-stu-id="19c0d-114">Open a Command Prompt window as an administrator</span></span>  
2. <span data-ttu-id="19c0d-115">ネイティブ イメージを生成するアセンブリの場所を現在の作業ディレクトリを変更します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-115">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>  

  ``` console
    cd <*Assemblies location*>  
  ```
3. <span data-ttu-id="19c0d-116">オペレーティング システムとアプリケーションの構成によっては、32 ビット アーキテクチャでは、64 ビット アーキテクチャ用、または両方のネイティブ イメージを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="19c0d-116">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>  

    <span data-ttu-id="19c0d-117">32 ビットの実行します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-117">For 32 bit run:</span></span>  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    <span data-ttu-id="19c0d-118">64 ビットの実行します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-118">For 64 bit run:</span></span>
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> <span data-ttu-id="19c0d-119">非常によくある間違いは、正しくないアーキテクチャのネイティブ イメージを生成します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-119">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="19c0d-120">ときに不明な単に、マシンにインストールされているオペレーティング システムに適用されるすべてのアーキテクチャ用のネイティブ イメージを生成することができます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-120">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="19c0d-121">NGen.exe をアンインストールして、複数のイメージ生成のキュー、インストールされたネイティブ イメージを表示するなどの他の関数もサポートします。使用状況の詳細については、読み取り、 [NGen.exe ドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-121">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="19c0d-122">NGen.exe を使用する場合</span><span class="sxs-lookup"><span data-stu-id="19c0d-122">When to use NGen.exe</span></span>  

<span data-ttu-id="19c0d-123">EF 6 以降のバージョンに基づくアプリケーションでのネイティブ イメージを生成するアセンブリを決定する際に、次のオプションを検討してください。</span><span class="sxs-lookup"><span data-stu-id="19c0d-123">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="19c0d-124">**メインの EF ランタイム アセンブリとなる EntityFramework.dll**: 一般的な EF ベースのアプリケーションをデータベースに起動時に、またはその最初のアクセスは、このアセンブリから大量のコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-124">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="19c0d-125">そのため、このアセンブリのネイティブ イメージを作成すると、起動時のパフォーマンスの向上が生成されます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-125">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="19c0d-126">**アプリケーションで使用される任意の EF プロバイダー アセンブリ**: 起動時メリットも若干これらのネイティブ イメージを生成します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-126">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="19c0d-127">たとえば、アプリケーション EF provider for SQL Server を使用するは場合は、EntityFramework.SqlServer.dll のネイティブ イメージを生成します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-127">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="19c0d-128">**アプリケーションのアセンブリとその他の依存関係**: [NGen.exe ドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx)のネイティブ イメージと、セキュリティ上のネイティブ イメージの影響を生成するアセンブリを選択するための一般的な条件について説明します「ハード バインディング」などのオプションを高度なデバッグとプロファイリング シナリオなどでのネイティブ イメージを使用してなどのシナリオ。</span><span class="sxs-lookup"><span data-stu-id="19c0d-128">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="19c0d-129">慎重に起動時のパフォーマンスと、アプリケーションの全体的なパフォーマンスの両方でネイティブ イメージを使用しての影響を測定して実際の要件と比較して確認します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-129">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="19c0d-130">ネイティブ イメージはスタートアップ パフォーマンスを向上させ、場合によっては、メモリ使用量を減らす際に役立つ一般的に、すべてのシナリオを均等に利用できます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-130">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="19c0d-131">たとえば、安定状態での実行時に (つまり、アプリケーションで使用されているすべてのメソッドが少なくとも 1 回呼び出された) と、JIT コンパイラによって生成されたコードこと実際に得ネイティブ イメージよりも若干優れたパフォーマンス。</span><span class="sxs-lookup"><span data-stu-id="19c0d-131">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="19c0d-132">開発用コンピューターで NGen.exe を使用します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-132">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="19c0d-133">.NET JIT の開発時にコンパイラが頻繁に変更するコードの最適な全体的なトレードオフが提供されます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-133">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="19c0d-134">EF のランタイム アセンブリなどのコンパイル済みの依存関係のネイティブ イメージを生成すると、開発とテストを実行するたびの先頭に数秒を切り抜くを高速化できます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-134">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="19c0d-135">EF のランタイム アセンブリを検索する点としては、ソリューションの NuGet パッケージの場所です。</span><span class="sxs-lookup"><span data-stu-id="19c0d-135">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="19c0d-136">たとえば、SQL Server での EF 6.0.2 を使用して、.NET 4.5 以降を対象とするアプリケーションのことができますを入力、次コマンド プロンプト ウィンドウで (管理者として開きます。 これを思い出してください)。</span><span class="sxs-lookup"><span data-stu-id="19c0d-136">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

``` console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="19c0d-137">これは、EF provider for SQL Server のネイティブ イメージをインストールすることも既定でインストールされますネイティブ イメージ、メインの EF ランタイム アセンブリを利用します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-137">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="19c0d-138">これは、NGen.exe が EntityFramework.dll が同じディレクトリにある EntityFramework.SqlServer.dll アセンブリの直接的な依存関係を検出できるために機能します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-138">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="19c0d-139">セットアップ時にネイティブ イメージの作成</span><span class="sxs-lookup"><span data-stu-id="19c0d-139">Creating native images during setup</span></span>  

<span data-ttu-id="19c0d-140">WiX ツールキットがこれで説明するようキューのセットアップ中に、マネージ アセンブリのネイティブ イメージの生成をサポート[ハウツー ガイド](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-140">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="19c0d-141">別の方法では、NGen.exe コマンドを実行するカスタム セットアップ タスクを作成します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-141">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="19c0d-142">EF のネイティブ イメージが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-142">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="19c0d-143">特定のアプリケーションが、拡張子が読み込まれたアセンブリを探すことによって、ネイティブ アセンブリを使用していることを確認できます". ni.dll「または」. ni.exe"。</span><span class="sxs-lookup"><span data-stu-id="19c0d-143">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="19c0d-144">たとえば、EF のメインのランタイム アセンブリのネイティブ イメージの名前は、EntityFramework.ni.dll には。</span><span class="sxs-lookup"><span data-stu-id="19c0d-144">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="19c0d-145">プロセスの読み込み済みの .NET アセンブリを検査する簡単な方法は、使用することです。 [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-145">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="19c0d-146">注意すべきこと</span><span class="sxs-lookup"><span data-stu-id="19c0d-146">Other things to be aware of</span></span>  

<span data-ttu-id="19c0d-147">**アセンブリのネイティブ イメージを作成する必要がありますと混同しないでアセンブリの登録、 [GAC (グローバル アセンブリ キャッシュ)](https://msdn.microsoft.com/library/yf1d93sz.aspx)** します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-147">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="19c0d-148">NGen.exe は、GAC に含まれていないアセンブリのイメージを作成して、実際には、特定のバージョンの EF を使用する複数のアプリケーションは、同じネイティブ イメージを共有できます。</span><span class="sxs-lookup"><span data-stu-id="19c0d-148">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="19c0d-149">Windows 8 では、GAC に配置するアセンブリのネイティブ イメージを自動的に作成できます、中に、アプリケーションと共に展開する EF ランタイムが最適化され、アセンブリの解決に悪影響を及ぼしますこれとは、GAC に登録しないで、その他の側面の間で、アプリケーションにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="19c0d-149">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  
