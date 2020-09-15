---
title: EF6 から EF Core への移植 - コードベース モデルの移植 - EF
description: Entity Framework 6 つのコードベース モデル アプリケーションを Entity Framework Core に移植する方法に関する固有の情報
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a5bbdc2ee95ea6bea96e24bee4588b524e0ffc58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073579"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="02200-103">EF Core への EF6 コードベース モデルの移植</span><span class="sxs-lookup"><span data-stu-id="02200-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="02200-104">すべての注意事項を読み、移植の準備ができたら、次のガイドラインを参考にして作業を開始してください。</span><span class="sxs-lookup"><span data-stu-id="02200-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="02200-105">EF Core の NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="02200-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="02200-106">EF Core を使用するには、使用するデータベース プロバイダーに対して NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="02200-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="02200-107">たとえば、SQL Server を対象とする場合は、`Microsoft.EntityFrameworkCore.SqlServer` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="02200-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="02200-108">詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02200-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="02200-109">移行の使用を計画している場合は、`Microsoft.EntityFrameworkCore.Tools` パッケージもインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="02200-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="02200-110">EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションで並列で使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="02200-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="02200-111">ただし、アプリケーションのどの領域でも EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコード上にコンパイル エラーを表示することができます。</span><span class="sxs-lookup"><span data-stu-id="02200-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="02200-112">名前空間をスワップする</span><span class="sxs-lookup"><span data-stu-id="02200-112">Swap namespaces</span></span>

<span data-ttu-id="02200-113">EF6 で使用する API のほとんどは、`System.Data.Entity` 名前空間 (および関連するサブ名前空間) にあります。</span><span class="sxs-lookup"><span data-stu-id="02200-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="02200-114">最初のコード変更では、`Microsoft.EntityFrameworkCore` 名前空間にスワップします。</span><span class="sxs-lookup"><span data-stu-id="02200-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="02200-115">通常は、派生コンテキスト コード ファイルから開始し、そこで作業を行い、コンパイル エラーが発生したら対処します。</span><span class="sxs-lookup"><span data-stu-id="02200-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="02200-116">コンテキストの構成 (接続など)</span><span class="sxs-lookup"><span data-stu-id="02200-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="02200-117">[EF Core がアプリケーションで動作するかどうかの確認](xref:efcore-and-ef6/porting/index)の記事で説明しているように、EF Core には、接続先のデータベースを検出する特別な方法はあまりありません。</span><span class="sxs-lookup"><span data-stu-id="02200-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="02200-118">派生コンテキストで `OnConfiguring` メソッドをオーバーライドし、API 固有のデータベース プロバイダーを使用してデータベースへの接続を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02200-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="02200-119">ほとんどの EF6 アプリケーションでは、接続文字列がアプリケーションの `App/Web.config` ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="02200-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="02200-120">EF Core では、`ConfigurationManager` API を使用してこの接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="02200-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="02200-121">この API を使用できるようにするには、`System.Configuration` フレームワーク アセンブリに参照を追加しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="02200-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="02200-122">コードを更新する</span><span class="sxs-lookup"><span data-stu-id="02200-122">Update your code</span></span>

<span data-ttu-id="02200-123">この時点では、コンパイル エラーに対処し、コードを見直して、動作変更の影響があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="02200-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="02200-124">既存の移行</span><span class="sxs-lookup"><span data-stu-id="02200-124">Existing migrations</span></span>

<span data-ttu-id="02200-125">既存の EF6 移行を EF Core に移植する方法は実際にはありません。</span><span class="sxs-lookup"><span data-stu-id="02200-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="02200-126">可能であれば、EF6 からの以前の移行がすべてデータベースに適用されていることを前提に、EF Core を使用してその時点からスキーマの移行を開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="02200-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="02200-127">これを行うには、モデルが EF Core に移植された後に `Add-Migration` コマンドを使用して移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="02200-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="02200-128">次に、スキャフォールディング移行の `Up` と `Down` のメソッドからすべてのコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="02200-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="02200-129">その後の移行では、最初の移行がスキャフォールディングされたときにモデルと比較されます。</span><span class="sxs-lookup"><span data-stu-id="02200-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="02200-130">移植をテストする</span><span class="sxs-lookup"><span data-stu-id="02200-130">Test the port</span></span>

<span data-ttu-id="02200-131">アプリケーションでコンパイルされるからといって、EF Core に正常に移植されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="02200-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="02200-132">アプリケーションのすべての領域をテストして、どの動作変更もアプリケーションに悪影響を与えないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02200-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
