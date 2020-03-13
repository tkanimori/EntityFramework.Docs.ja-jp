---
title: EF6 と EF Core - 同じアプリケーションでの使用
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: bcf0a26535c4ec880a9ac25478c987fb683f6d26
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413865"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>同じアプリケーションでの EF Core と EF6 の使用

両方の NuGet パッケージをインストールすることで、EF Core と EF6 を同じアプリケーションやライブラリで使用できます。

一部の型は EF Core と EF6 で名前が同じで、名前空間のみが異なります。そのために、EF Core と EF6 の両方を同じコード ファイルで使用するのが複雑になる場合もあります。 名前空間エイリアス ディレクティブを使用すると、このあいまいさを簡単に無くすことができます。 次に例を示します。

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

複数の EF モデルを持つ既存のアプリケーションを移植する場合には、一部を EF Core に移植し、その他では EF6 を使い続けることを選択できます。
