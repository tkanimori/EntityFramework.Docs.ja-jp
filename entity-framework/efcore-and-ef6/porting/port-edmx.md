---
title: EF6 から EF Core への移植 - EDMX ベース モデルの移植 - EF
description: Entity Framework 6 つの EDMX ベース モデル アプリケーションを Entity Framework Core に移植する方法に関する固有の情報
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 27b37ad1c2e3436ae96a71bc97e953763c48ee50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064264"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="ce797-103">EF Core への EF6 EDMX ベース モデルの移植</span><span class="sxs-lookup"><span data-stu-id="ce797-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="ce797-104">EF Core では EDMX ファイル形式のモデルをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="ce797-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="ce797-105">これらのモデルを移植する最良の方法は、アプリケーションのデータベースから新しいコードベースのモデルを生成することです。</span><span class="sxs-lookup"><span data-stu-id="ce797-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="ce797-106">EF Core の NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="ce797-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="ce797-107">`Microsoft.EntityFrameworkCore.Tools` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ce797-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="ce797-108">モデルを再生成する</span><span class="sxs-lookup"><span data-stu-id="ce797-108">Regenerate the model</span></span>

<span data-ttu-id="ce797-109">リバース エンジニアリング機能を使用して、既存のデータベースに基づくモデルを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ce797-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="ce797-110">パッケージ マネージャー コンソールで [ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール] のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ce797-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="ce797-111">テーブルのサブセットをスキャフォールディングするコマンド オプションなどについては、[Visual Studio のパッケージ マネージャー コンソール](xref:core/miscellaneous/cli/powershell)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce797-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="ce797-112">たとえば、SQL Server LocalDB インスタンスのブログ データベースからモデルをスキャフォールディングするコマンドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="ce797-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="ce797-113">EF6 モデルを削除する</span><span class="sxs-lookup"><span data-stu-id="ce797-113">Remove EF6 model</span></span>

<span data-ttu-id="ce797-114">ここでは、アプリケーションから EF6 モデルを削除します。</span><span class="sxs-lookup"><span data-stu-id="ce797-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="ce797-115">EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションで並列で使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="ce797-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="ce797-116">ただし、アプリケーションのどの領域でも EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコード上にコンパイル エラーを表示することができます。</span><span class="sxs-lookup"><span data-stu-id="ce797-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="ce797-117">コードを更新する</span><span class="sxs-lookup"><span data-stu-id="ce797-117">Update your code</span></span>

<span data-ttu-id="ce797-118">この時点では、コンパイル エラーに対処し、コードを見直して、EF6 から EF Core への動作変更の影響があるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="ce797-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="ce797-119">移植をテストする</span><span class="sxs-lookup"><span data-stu-id="ce797-119">Test the port</span></span>

<span data-ttu-id="ce797-120">アプリケーションでコンパイルされるからといって、EF Core に正常に移植されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="ce797-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="ce797-121">アプリケーションのすべての領域をテストして、どの動作変更もアプリケーションに悪影響を与えないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ce797-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
