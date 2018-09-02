---
title: EF6 と EF Core - 同じアプリケーションでの使用
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 6f95c02f4f24746605794832b1e26744fc554580
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995710"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="12394-102">同じアプリケーションでの EF Core と EF6 の使用</span><span class="sxs-lookup"><span data-stu-id="12394-102">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="12394-103">両方の NuGet パッケージをインストールすることで、EF Core と EF6 を同じ .NET Framework アプリケーションやライブラリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="12394-103">It is possible to use EF Core and EF6 in the same .NET Framework application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="12394-104">一部の型は EF Core と EF6 で名前が同じで、名前空間のみが異なります。そのために、EF Core と EF6 の両方を同じコード ファイルで使用するのが複雑になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="12394-104">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="12394-105">名前空間エイリアス ディレクティブを使用すると、このあいまいさを簡単に無くすことができます。</span><span class="sxs-lookup"><span data-stu-id="12394-105">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="12394-106">例:</span><span class="sxs-lookup"><span data-stu-id="12394-106">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="12394-107">複数の EF モデルを持つ既存のアプリケーションを移植する場合には、一部を EF Core に移植し、その他では EF6 を使い続けることを選択できます。</span><span class="sxs-lookup"><span data-stu-id="12394-107">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
