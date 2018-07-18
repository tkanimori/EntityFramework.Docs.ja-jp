---
title: EF6 と EF Core - 同じアプリケーションでの使用
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: ead251c5454473738c2f2bfdac6557aa3e1c5591
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949078"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>同じアプリケーションでの EF Core と EF6 の使用

両方の NuGet パッケージをインストールすることで、EF Core と EF6 を同じ .NET Framework アプリケーションやライブラリで使用できます。

一部の型は EF Core と EF6 で名前が同じで、名前空間のみが異なります。そのために、EF Core と EF6 の両方を同じコード ファイルで使用するのが複雑になる場合もあります。 名前空間エイリアス ディレクティブを使用すると、このあいまいさを簡単に無くすことができます。 例:

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

複数の EF モデルを持つ既存のアプリケーションを移植する場合には、一部を EF Core に移植し、その他では EF6 を使い続けることを選択できます。
