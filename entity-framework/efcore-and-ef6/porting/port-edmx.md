---
title: EF6 から EF Core に移植して、EDMX ベースのモデルを移植する
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: a1c978e141f47a39fff59eb5fc417a63afd37b04
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71148998"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="345b6-102">EF6 EDMX ベースのモデルを EF Core に移植する</span><span class="sxs-lookup"><span data-stu-id="345b6-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="345b6-103">EF Core は、モデルの EDMX ファイル形式をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="345b6-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="345b6-104">これらのモデルを移植する最良の方法は、アプリケーションのデータベースから新しいコードベースのモデルを生成することです。</span><span class="sxs-lookup"><span data-stu-id="345b6-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="345b6-105">NuGet パッケージのインストール EF Core</span><span class="sxs-lookup"><span data-stu-id="345b6-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="345b6-106">`Microsoft.EntityFrameworkCore.Tools` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="345b6-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="345b6-107">モデルの再生成</span><span class="sxs-lookup"><span data-stu-id="345b6-107">Regenerate the model</span></span>

<span data-ttu-id="345b6-108">リバースエンジニアリング機能を使用して、既存のデータベースに基づいてモデルを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="345b6-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="345b6-109">パッケージマネージャーコンソールで次のコマンドを実行します ([ツール] – > NuGet パッケージマネージャー– > パッケージマネージャーコンソール)。</span><span class="sxs-lookup"><span data-stu-id="345b6-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="345b6-110">テーブルのサブセットをスキャフォールディングするためのコマンドオプションについては、「[パッケージマネージャーコンソール (Visual Studio)](../../core/miscellaneous/cli/powershell.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="345b6-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="345b6-111">たとえば、SQL Server LocalDB インスタンスのブログデータベースからモデルをスキャフォールディングするコマンドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="345b6-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="345b6-112">EF6 モデルの削除</span><span class="sxs-lookup"><span data-stu-id="345b6-112">Remove EF6 model</span></span>

<span data-ttu-id="345b6-113">ここで、アプリケーションから EF6 モデルを削除します。</span><span class="sxs-lookup"><span data-stu-id="345b6-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="345b6-114">EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションでサイドバイサイドで使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="345b6-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="345b6-115">ただし、アプリケーションの任意の領域で EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコードの一部でコンパイルエラーが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="345b6-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="345b6-116">コードを更新する</span><span class="sxs-lookup"><span data-stu-id="345b6-116">Update your code</span></span>

<span data-ttu-id="345b6-117">この時点で、コンパイルエラーに対処し、コードを確認して、EF6 と EF Core 間で動作が変化するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="345b6-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="345b6-118">ポートをテストする</span><span class="sxs-lookup"><span data-stu-id="345b6-118">Test the port</span></span>

<span data-ttu-id="345b6-119">アプリケーションがコンパイルされるので、は EF Core に正常に移植されるという意味ではありません。</span><span class="sxs-lookup"><span data-stu-id="345b6-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="345b6-120">アプリケーションのすべての領域をテストして、動作の変更がアプリケーションに悪影響を与えないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="345b6-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
