---
title: EF6 から EF Core への移植 - コード ベースのモデルの移植
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 2484b681d71ae8711b1b3a59bc274a0b2e403294
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997050"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="67c8b-102">EF Core に EF6 コードに基づくモデルの移植</span><span class="sxs-lookup"><span data-stu-id="67c8b-102">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="67c8b-103">すべての警告を読んだし、ポートに準備ができたら、し、ここではいくつかのガイドラインを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67c8b-103">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="67c8b-104">EF Core NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-104">Install EF Core NuGet packages</span></span>

<span data-ttu-id="67c8b-105">EF Core を使用するには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-105">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="67c8b-106">たとえば、SQL Server を対象とするときにインストール`Microsoft.EntityFrameworkCore.SqlServer`します。</span><span class="sxs-lookup"><span data-stu-id="67c8b-106">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="67c8b-107">参照してください[データベース プロバイダー](../../core/providers/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-107">See [Database Providers](../../core/providers/index.md) for details.</span></span>

<span data-ttu-id="67c8b-108">移行を使用する予定のかどうかは、インストールする必要があります、`Microsoft.EntityFrameworkCore.Tools`パッケージ。</span><span class="sxs-lookup"><span data-stu-id="67c8b-108">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="67c8b-109">これを EF6 の NuGet パッケージ (EntityFramework) をインストールすると、そのままに EF Core と EF6 が同じアプリケーションで並列で使用できます。</span><span class="sxs-lookup"><span data-stu-id="67c8b-109">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="67c8b-110">ただし、アプリケーションの領域で EF6 を使用する場合は、パッケージをアンインストールし、役立つの注意が必要なコードのコンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="67c8b-110">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="67c8b-111">名前空間をスワップします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-111">Swap namespaces</span></span>

<span data-ttu-id="67c8b-112">EF6 で使用するほとんどの Api は、`System.Data.Entity`名前空間 (および関連するサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="67c8b-112">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="67c8b-113">最初のコード変更をスワップするには、`Microsoft.EntityFrameworkCore`名前空間。</span><span class="sxs-lookup"><span data-stu-id="67c8b-113">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="67c8b-114">通常、派生コンテキストのコード ファイルを起動し、作業するそこが発生したコンパイル エラーに対処します。</span><span class="sxs-lookup"><span data-stu-id="67c8b-114">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="67c8b-115">コンテキストの構成 (接続など)</span><span class="sxs-lookup"><span data-stu-id="67c8b-115">Context configuration (connection etc.)</span></span>

<span data-ttu-id="67c8b-116">」の説明に従って[ように EF Core は作業アプリケーションの](ensure-requirements.md)、EF Core が少ないマジックの周囲に接続するデータベースを検出します。</span><span class="sxs-lookup"><span data-stu-id="67c8b-116">As described in [Ensure EF Core Will Work for Your Application](ensure-requirements.md), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="67c8b-117">オーバーライドする必要があります、`OnConfiguring`メソッドを派生コンテキストと、データベースへの接続をセットアップするには、データベース プロバイダーの特定の API を使用します。</span><span class="sxs-lookup"><span data-stu-id="67c8b-117">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="67c8b-118">ほとんどの EF6 アプリケーションでは、アプリケーションの接続文字列を格納`App/Web.config`ファイル。</span><span class="sxs-lookup"><span data-stu-id="67c8b-118">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="67c8b-119">EF Core でのこの接続文字列を使用して読み取りを`ConfigurationManager`API。</span><span class="sxs-lookup"><span data-stu-id="67c8b-119">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="67c8b-120">参照を追加する必要があります、`System.Configuration`この API を使用できるフレームワーク アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="67c8b-120">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="67c8b-121">コードを更新します</span><span class="sxs-lookup"><span data-stu-id="67c8b-121">Update your code</span></span>

<span data-ttu-id="67c8b-122">この時点では、コンパイル エラーをアドレス指定と確認の動作の変更とするかどうかは影響を確認するコードの問題になります。</span><span class="sxs-lookup"><span data-stu-id="67c8b-122">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="67c8b-123">既存の移行</span><span class="sxs-lookup"><span data-stu-id="67c8b-123">Existing migrations</span></span>

<span data-ttu-id="67c8b-124">既存の EF6 の移行を EF Core に移植可能な方法が本当にありません。</span><span class="sxs-lookup"><span data-stu-id="67c8b-124">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="67c8b-125">可能であれば、EF6 からすべての以前の移行をデータベースに適用されているし、開始からスキーマを移行するポイントの EF Core を使用しが想定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-125">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="67c8b-126">これを行うには、使用して、`Add-Migration`モデルが EF Core に移植したら、移行を追加するコマンド。</span><span class="sxs-lookup"><span data-stu-id="67c8b-126">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="67c8b-127">すべてのコードを削除し、`Up`と`Down`スキャフォールディングの移行の方法です。</span><span class="sxs-lookup"><span data-stu-id="67c8b-127">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="67c8b-128">次の移行は、その最初の移行がスキャフォールディングされるときに、モデルに比較されます。</span><span class="sxs-lookup"><span data-stu-id="67c8b-128">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="67c8b-129">ポートをテストします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-129">Test the port</span></span>

<span data-ttu-id="67c8b-130">アプリケーションをコンパイルするためにだけしないわけでは EF Core に移植が正常にします。</span><span class="sxs-lookup"><span data-stu-id="67c8b-130">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="67c8b-131">None、動作の変更と、アプリケーションが悪影響を受けることを確認するアプリケーションのすべての領域をテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="67c8b-131">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
