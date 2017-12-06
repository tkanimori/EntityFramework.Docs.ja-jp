---
title: "EF6 から EF Core - 移植 EDMX ベースのモデルへの移植"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: c999d2114c76ee3a7615ae897b42ee3376cff14e
ms.sourcegitcommit: 1880d5ce49d4c9cb891d0e8fb230770bb44799e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="f3e91-102">EF core EF6 EDMX に基づくモデルの移植</span><span class="sxs-lookup"><span data-stu-id="f3e91-102">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="f3e91-103">EF Core では、モデルの EDMX ファイルの形式はサポートしません。</span><span class="sxs-lookup"><span data-stu-id="f3e91-103">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="f3e91-104">これらのモデルに移植する最適なオプションでは、アプリケーションのデータベースから新しいコードに基づくモデルを生成します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-104">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="f3e91-105">EF Core NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f3e91-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="f3e91-106">インストール、 `Microsoft.EntityFrameworkCore.Tools` NuGet パッケージです。</span><span class="sxs-lookup"><span data-stu-id="f3e91-106">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="f3e91-107">モデルを再生成します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-107">Regenerate the model</span></span>

<span data-ttu-id="f3e91-108">既存のデータベースに基づくモデルを作成する、リバース エンジニア リングの機能を使えるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f3e91-108">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="f3e91-109">パッケージ マネージャー コンソールで、次のコマンドを実行 (ツールは NuGet Package Manager をポイント パッケージ マネージャー コンソールのポイント)。</span><span class="sxs-lookup"><span data-stu-id="f3e91-109">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="f3e91-110">参照してください[Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md)テーブルなどのサブセットをスキャフォールディングするコマンド オプションについてはします。</span><span class="sxs-lookup"><span data-stu-id="f3e91-110">See [Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="f3e91-111">たとえば、SQL Server LocalDB インスタンス上のブログ データベースからモデルをスキャフォールディングするコマンドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-111">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="f3e91-112">EF6 モデルを削除します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-112">Remove EF6 model</span></span>

<span data-ttu-id="f3e91-113">今すぐ、アプリケーションから EF6 モデルを削除します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-113">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="f3e91-114">これは正常に EF6 NuGet パッケージ (EntityFramework) をインストールすると、そのまま EF コアおよび EF6 が同じアプリケーションに並列で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3e91-114">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="f3e91-115">ただし、アプリケーションのすべての領域で EF6 を使用する場合は、パッケージをアンインストールに役立つの注意が必要なコードのコンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-115">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="f3e91-116">コードを更新します</span><span class="sxs-lookup"><span data-stu-id="f3e91-116">Update your code</span></span>

<span data-ttu-id="f3e91-117">この時点では、アドレス指定のコンパイル エラーと EF6 と EF コア間の動作の変更に影響するかどうかを確認するコードの問題であります。</span><span class="sxs-lookup"><span data-stu-id="f3e91-117">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="f3e91-118">ポートをテストします。</span><span class="sxs-lookup"><span data-stu-id="f3e91-118">Test the port</span></span>

<span data-ttu-id="f3e91-119">アプリケーションをコンパイルするためにだけ限りません EF コアが正常に移植します。</span><span class="sxs-lookup"><span data-stu-id="f3e91-119">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="f3e91-120">動作の変更のいずれもと、アプリケーションが悪影響を受けることを確認するには、アプリケーションのすべての領域をテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3e91-120">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>

> [!TIP]
> <span data-ttu-id="f3e91-121">参照してください[EF Core 既存のデータベースでの ASP.NET Core の使用を開始する](xref:core/get-started/aspnetcore/existing-db)既存のデータベースを操作する方法に関するその他のリファレンスについては</span><span class="sxs-lookup"><span data-stu-id="f3e91-121">See [Getting Started with EF Core on ASP.NET Core with an Existing Database](xref:core/get-started/aspnetcore/existing-db) for an additional reference on how to work with an existing database,</span></span> 
