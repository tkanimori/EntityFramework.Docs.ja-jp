---
title: EF6 から EF Core への移植 - コードベース モデルの移植 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413859"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="05760-102">EF Core への EF6 コードベース モデルの移植</span><span class="sxs-lookup"><span data-stu-id="05760-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="05760-103">すべての注意事項を読み、移植の準備ができたら、次のガイドラインを参考にして作業を開始してください。</span><span class="sxs-lookup"><span data-stu-id="05760-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="05760-104">EF Core の NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="05760-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="05760-105">EF Core を使用するには、使用するデータベース プロバイダーに対して NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="05760-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="05760-106">たとえば、SQL Server を対象とする場合は、`Microsoft.EntityFrameworkCore.SqlServer` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="05760-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="05760-107">詳細については、「[データベース プロバイダー](../../core/providers/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="05760-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="05760-108">移行の使用を計画している場合は、`Microsoft.EntityFrameworkCore.Tools` パッケージもインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="05760-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="05760-109">EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションで並列で使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="05760-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="05760-110">ただし、アプリケーションのどの領域でも EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコード上にコンパイル エラーを表示することができます。</span><span class="sxs-lookup"><span data-stu-id="05760-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="05760-111">名前空間をスワップする</span><span class="sxs-lookup"><span data-stu-id="05760-111">Swap namespaces</span></span>

<span data-ttu-id="05760-112">EF6 で使用する API のほとんどは、`System.Data.Entity` 名前空間 (および関連するサブ名前空間) にあります。</span><span class="sxs-lookup"><span data-stu-id="05760-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="05760-113">最初のコード変更では、`Microsoft.EntityFrameworkCore` 名前空間にスワップします。</span><span class="sxs-lookup"><span data-stu-id="05760-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="05760-114">通常は、派生コンテキスト コード ファイルから開始し、そこで作業を行い、コンパイル エラーが発生したら対処します。</span><span class="sxs-lookup"><span data-stu-id="05760-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="05760-115">コンテキストの構成 (接続など)</span><span class="sxs-lookup"><span data-stu-id="05760-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="05760-116">[EF Core がアプリケーションで動作するかどうかの確認](ensure-requirements.md)の記事で説明しているように、EF Core には、接続先のデータベースを検出する特別な方法はあまりありません。</span><span class="sxs-lookup"><span data-stu-id="05760-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="05760-117">派生コンテキストで `OnConfiguring` メソッドをオーバーライドし、API 固有のデータベース プロバイダーを使用してデータベースへの接続を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05760-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="05760-118">ほとんどの EF6 アプリケーションでは、接続文字列がアプリケーションの `App/Web.config` ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="05760-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="05760-119">EF Core では、`ConfigurationManager` API を使用してこの接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="05760-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="05760-120">この API を使用できるようにするには、`System.Configuration` フレームワーク アセンブリに参照を追加しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="05760-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="05760-121">コードを更新する</span><span class="sxs-lookup"><span data-stu-id="05760-121">Update your code</span></span>

<span data-ttu-id="05760-122">この時点では、コンパイル エラーに対処し、コードを見直して、動作変更の影響があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="05760-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="05760-123">既存の移行</span><span class="sxs-lookup"><span data-stu-id="05760-123">Existing migrations</span></span>

<span data-ttu-id="05760-124">既存の EF6 移行を EF Core に移植する方法は実際にはありません。</span><span class="sxs-lookup"><span data-stu-id="05760-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="05760-125">可能であれば、EF6 からの以前の移行がすべてデータベースに適用されていることを前提に、EF Core を使用してその時点からスキーマの移行を開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="05760-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="05760-126">これを行うには、モデルが EF Core に移植された後に `Add-Migration` コマンドを使用して移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="05760-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="05760-127">次に、スキャフォールディング移行の `Up` と `Down` のメソッドからすべてのコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="05760-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="05760-128">その後の移行では、最初の移行がスキャフォールディングされたときにモデルと比較されます。</span><span class="sxs-lookup"><span data-stu-id="05760-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="05760-129">移植をテストする</span><span class="sxs-lookup"><span data-stu-id="05760-129">Test the port</span></span>

<span data-ttu-id="05760-130">アプリケーションでコンパイルされるからといって、EF Core に正常に移植されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="05760-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="05760-131">アプリケーションのすべての領域をテストして、どの動作変更もアプリケーションに悪影響を与えないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05760-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
