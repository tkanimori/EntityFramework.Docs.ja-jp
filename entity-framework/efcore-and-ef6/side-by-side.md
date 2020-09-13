---
title: EF6 と EF Core - 同じアプリケーションでの使用
description: 同じアプリケーションでの Entity Framework Core および Entity Framework 6 の両方の使用に関するガイダンス
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 9bd3d837a333eb23be4cb3095b7f387ad303376d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619557"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="effe4-103">同じアプリケーションでの EF Core と EF6 の使用</span><span class="sxs-lookup"><span data-stu-id="effe4-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="effe4-104">両方の NuGet パッケージをインストールすることで、EF Core と EF6 を同じアプリケーションやライブラリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="effe4-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="effe4-105">一部の型は EF Core と EF6 で名前が同じで、名前空間のみが異なります。そのために、EF Core と EF6 の両方を同じコード ファイルで使用するのが複雑になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="effe4-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="effe4-106">名前空間エイリアス ディレクティブを使用すると、このあいまいさを簡単に無くすことができます。</span><span class="sxs-lookup"><span data-stu-id="effe4-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="effe4-107">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="effe4-107">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="effe4-108">複数の EF モデルを持つ既存のアプリケーションを移植する場合には、一部を EF Core に移植し、その他では EF6 を使い続けることを選択できます。</span><span class="sxs-lookup"><span data-stu-id="effe4-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
