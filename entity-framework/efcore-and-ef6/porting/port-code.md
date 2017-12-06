---
title: "EF6 から EF Core - コードに基づくモデルを移植への移植"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a0fa4f9a7028f56666fb993185cb03eddb9a2cd1
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="f2ecf-102">EF core EF6 コードに基づくモデルの移植</span><span class="sxs-lookup"><span data-stu-id="f2ecf-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="f2ecf-103">すべての警告を読んだし、ポートする準備ができたら、し、ここでは作業を開始するためのいくつかのガイドライン。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="f2ecf-104">EF Core NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="f2ecf-105">EF コアを使用するのには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="f2ecf-106">たとえば、SQL Server を対象とする場合にインストールする場合`Microsoft.EntityFrameworkCore.SqlServer`です。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="f2ecf-107">参照してください[データベース プロバイダー](../../core/providers/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="f2ecf-108">移行を使用する予定のかどうかは、インストールすることも必要があります、`Microsoft.EntityFrameworkCore.Tools`パッケージです。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="f2ecf-109">これは正常に EF6 NuGet パッケージ (EntityFramework) をインストールすると、そのまま EF コアおよび EF6 が同じアプリケーションに並列で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="f2ecf-110">ただし、アプリケーションのすべての領域で EF6 を使用する場合は、パッケージをアンインストールに役立つの注意が必要なコードのコンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="f2ecf-111">名前空間をスワップします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-111">Swap namespaces</span></span>

<span data-ttu-id="f2ecf-112">EF6 で使用するほとんどの Api は、`System.Data.Entity`名前空間 (および関連するサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="f2ecf-113">最初のコード変更をスワップするには、`Microsoft.EntityFrameworkCore`名前空間。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="f2ecf-114">通常、派生コンテキスト コード ファイルで開始し、作業そこから発生すると、コンパイル エラーを修正とします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="f2ecf-115">コンテキストの構成 (接続などです。)</span><span class="sxs-lookup"><span data-stu-id="f2ecf-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="f2ecf-116">」の説明に従って[を確認してください EF コアは作業アプリケーションの](ensure-requirements.md)、EF コアが少ないマジック周囲への接続にデータベースを検出します。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="f2ecf-117">オーバーライドする必要があります、`OnConfiguring`メソッドを派生のコンテキストと、データベースへの接続をセットアップするには、データベース プロバイダーの特定の API を使用します。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="f2ecf-118">ほとんどの EF6 アプリケーションは、アプリケーションでは、接続文字列を格納`App/Web.config`ファイル。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="f2ecf-119">EF コアで読むこの接続文字列を使用して、 `ConfigurationManager` API です。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="f2ecf-120">参照を追加する必要があります、`System.Configuration`フレームワーク アセンブリをこの API を使用することです。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a><span data-ttu-id="f2ecf-121">コードを更新します</span><span class="sxs-lookup"><span data-stu-id="f2ecf-121">Update your code</span></span>

<span data-ttu-id="f2ecf-122">この時点でのコンパイル エラーを修正するかどうか、動作は変更に影響するを参照してください。 コードを確認することを勧めします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="f2ecf-123">既存の移行</span><span class="sxs-lookup"><span data-stu-id="f2ecf-123">Existing migrations</span></span>

<span data-ttu-id="f2ecf-124">方法はありません実際に可能なポートの EF コアへの既存の EF6 移行します。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="f2ecf-125">可能であれば、EF6 から以前のすべての移行は、データベースに適用されているし、スキーマを移行することから開始ポイント EF コアを使用したと仮定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="f2ecf-126">これを行うには、使用すると、 `Add-Migration` EF コアにモデルが移植された後に、移行を追加するコマンド。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="f2ecf-127">すべてのコードを削除すると、`Up`と`Down`スキャフォールディングの移行のメソッドです。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="f2ecf-128">次の移行は、その最初の移行がスキャフォールディングされたときにモデルに比較されます。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="f2ecf-129">ポートをテストします。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-129">Test the port</span></span>

<span data-ttu-id="f2ecf-130">アプリケーションをコンパイルするためにだけ限りません EF コアが正常に移植します。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="f2ecf-131">動作の変更のいずれもと、アプリケーションが悪影響を受けることを確認するには、アプリケーションのすべての領域をテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f2ecf-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
