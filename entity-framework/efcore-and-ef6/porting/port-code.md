---
title: EF6 から EF Core に移植してコードベースのモデルを移植する-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181215"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="70d56-102">EF6 コードベースモデルを EF Core に移植する</span><span class="sxs-lookup"><span data-stu-id="70d56-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="70d56-103">すべての注意事項を読んだ後に移植する準備ができたら、次のガイドラインを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="70d56-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="70d56-104">NuGet パッケージのインストール EF Core</span><span class="sxs-lookup"><span data-stu-id="70d56-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="70d56-105">EF Core を使用するには、使用するデータベースプロバイダーの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="70d56-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="70d56-106">たとえば、SQL Server を対象とする場合は、`Microsoft.EntityFrameworkCore.SqlServer` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="70d56-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="70d56-107">詳細については、「[データベースプロバイダー](../../core/providers/index.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="70d56-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="70d56-108">移行の使用を計画している場合は、@no__t 0 のパッケージもインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="70d56-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="70d56-109">EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションでサイドバイサイドで使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="70d56-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="70d56-110">ただし、アプリケーションの任意の領域で EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコードの一部でコンパイルエラーが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="70d56-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="70d56-111">名前空間の入れ替え</span><span class="sxs-lookup"><span data-stu-id="70d56-111">Swap namespaces</span></span>

<span data-ttu-id="70d56-112">EF6 で使用する Api のほとんどは、`System.Data.Entity` 名前空間 (および関連するサブ名前空間) にあります。</span><span class="sxs-lookup"><span data-stu-id="70d56-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="70d56-113">最初のコード変更では、@no__t 0 の名前空間にスワップします。</span><span class="sxs-lookup"><span data-stu-id="70d56-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="70d56-114">通常は、派生コンテキストコードファイルから開始し、そこから作業を行い、コンパイルエラーが発生したときに対処します。</span><span class="sxs-lookup"><span data-stu-id="70d56-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="70d56-115">コンテキストの構成 (接続など)</span><span class="sxs-lookup"><span data-stu-id="70d56-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="70d56-116">「アプリケーションで[EF Core が機能することを確認](ensure-requirements.md)する」で説明されているように、EF Core は、接続先のデータベースを検出するマジックがあまりありません。</span><span class="sxs-lookup"><span data-stu-id="70d56-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="70d56-117">派生コンテキストで `OnConfiguring` メソッドをオーバーライドし、データベースプロバイダー固有の API を使用してデータベースへの接続を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="70d56-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="70d56-118">ほとんどの EF6 アプリケーションは、接続文字列をアプリケーション `App/Web.config` ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="70d56-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="70d56-119">EF Core では、`ConfigurationManager` API を使用してこの接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="70d56-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="70d56-120">この API を使用できるようにするには、`System.Configuration` framework アセンブリに参照を追加する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="70d56-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="70d56-121">コードを更新する</span><span class="sxs-lookup"><span data-stu-id="70d56-121">Update your code</span></span>

<span data-ttu-id="70d56-122">この時点で、コンパイルエラーに対処し、コードを見直して、動作の変更が影響を受けるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="70d56-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="70d56-123">既存の移行</span><span class="sxs-lookup"><span data-stu-id="70d56-123">Existing migrations</span></span>

<span data-ttu-id="70d56-124">既存の EF6 移行を EF Core に移植する方法は実際にはありません。</span><span class="sxs-lookup"><span data-stu-id="70d56-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="70d56-125">可能であれば、EF6 からの以前の移行がすべてデータベースに適用されていることを前提にしてから、EF Core を使用してその時点からスキーマの移行を開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="70d56-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="70d56-126">これを行うには、モデルが EF Core に移植された後に、`Add-Migration` コマンドを使用して移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="70d56-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="70d56-127">次に、スキャフォールディング移行の `Up` および `Down` メソッドからすべてのコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="70d56-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="70d56-128">その後の移行では、最初の移行がスキャフォールディングされたときにモデルと比較されます。</span><span class="sxs-lookup"><span data-stu-id="70d56-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="70d56-129">ポートをテストする</span><span class="sxs-lookup"><span data-stu-id="70d56-129">Test the port</span></span>

<span data-ttu-id="70d56-130">アプリケーションがコンパイルされるので、は EF Core に正常に移植されるという意味ではありません。</span><span class="sxs-lookup"><span data-stu-id="70d56-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="70d56-131">アプリケーションのすべての領域をテストして、動作の変更がアプリケーションに悪影響を与えないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="70d56-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
