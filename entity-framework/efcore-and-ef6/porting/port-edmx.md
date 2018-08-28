---
title: EF6 から EF Core への移植 - EDMX ベースのモデルの移植
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 2c3336ac675a830566001a0ddb3777839f52db18
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997412"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="115c6-102">EF Core に EF6 EDMX ベース モデルの移植</span><span class="sxs-lookup"><span data-stu-id="115c6-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="115c6-103">EF Core は、モデルの EDMX ファイルの形式をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="115c6-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="115c6-104">これらのモデルに移植する最適なオプションでは、アプリケーションのデータベースから新しいコード ベースのモデルを生成します。</span><span class="sxs-lookup"><span data-stu-id="115c6-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="115c6-105">EF Core NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="115c6-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="115c6-106">インストール、 `Microsoft.EntityFrameworkCore.Tools` NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="115c6-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="115c6-107">モデルを再生成します。</span><span class="sxs-lookup"><span data-stu-id="115c6-107">Regenerate the model</span></span>

<span data-ttu-id="115c6-108">既存のデータベースに基づくモデルを作成する、リバース エンジニア リング機能を使えるようになりました。</span><span class="sxs-lookup"><span data-stu-id="115c6-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="115c6-109">パッケージ マネージャー コンソールで、次のコマンドを実行 (ツールが NuGet パッケージ マネージャーを -> パッケージ マネージャー コンソールを ->)。</span><span class="sxs-lookup"><span data-stu-id="115c6-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="115c6-110">参照してください[パッケージ マネージャー コンソール (Visual Studio)](../../core/miscellaneous/cli/powershell.md)コマンド オプションのテーブルなどのサブセットをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="115c6-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="115c6-111">たとえば、SQL Server LocalDB インスタンスで Blogging データベースからモデルをスキャフォールディングするコマンドを示します。</span><span class="sxs-lookup"><span data-stu-id="115c6-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="115c6-112">EF6 モデルを削除します。</span><span class="sxs-lookup"><span data-stu-id="115c6-112">Remove EF6 model</span></span>

<span data-ttu-id="115c6-113">EF6 モデルをアプリケーションから削除するとようになりました。</span><span class="sxs-lookup"><span data-stu-id="115c6-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="115c6-114">これを EF6 の NuGet パッケージ (EntityFramework) をインストールすると、そのままに EF Core と EF6 が同じアプリケーションで並列で使用できます。</span><span class="sxs-lookup"><span data-stu-id="115c6-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="115c6-115">ただし、アプリケーションの領域で EF6 を使用する場合は、パッケージをアンインストールし、役立つの注意が必要なコードのコンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="115c6-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="115c6-116">コードを更新します</span><span class="sxs-lookup"><span data-stu-id="115c6-116">Update your code</span></span>

<span data-ttu-id="115c6-117">この時点では、コンパイル エラーをアドレス指定と EF6 と EF Core の動作の変更に影響するかどうかを確認コードの問題になります。</span><span class="sxs-lookup"><span data-stu-id="115c6-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="115c6-118">ポートをテストします。</span><span class="sxs-lookup"><span data-stu-id="115c6-118">Test the port</span></span>

<span data-ttu-id="115c6-119">アプリケーションをコンパイルするためにだけしないわけでは EF Core に移植が正常にします。</span><span class="sxs-lookup"><span data-stu-id="115c6-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="115c6-120">None、動作の変更と、アプリケーションが悪影響を受けることを確認するアプリケーションのすべての領域をテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="115c6-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>

> [!TIP]
> <span data-ttu-id="115c6-121">参照してください[既存のデータベースでの ASP.NET Core での EF Core の概要](xref:core/get-started/aspnetcore/existing-db)既存のデータベースを操作する方法に関するその他のリファレンスについては</span><span class="sxs-lookup"><span data-stu-id="115c6-121">See [Getting Started with EF Core on ASP.NET Core with an Existing Database](xref:core/get-started/aspnetcore/existing-db) for an additional reference on how to work with an existing database,</span></span> 
