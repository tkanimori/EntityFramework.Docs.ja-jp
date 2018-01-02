---
title: "IBM Data Server (DB2) データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/15/2017
ms.assetid: 825e5332-5aa3-4600-9efb-ab71aaff59ec
ms.technology: entity-framework-core
uid: core/providers/ibm/index
ms.openlocfilehash: a9caa8df63850d4f6b5f2164dad7ac5af7504076
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="ibm-data-server-db2-ef-core-database-providers"></a>IBM Data Server (DB2) EF Core データベース プロバイダー

このデータベース プロバイダーにより、IBM データ サーバーと共に Entity Framework Core を使用できます。 このプロバイダーは IBM によって保守管理されます。

> [!NOTE]  
> このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。

## <a name="install"></a>Install

Windows 上で IBM データ サーバーを操作するには、[IBM.EntityFrameworkCore NuGet パッケージ](https://www.nuget.org/packages/IBM.EntityFrameworkCore)をインストールします。

``` powershell
Install-Package IBM.EntityFrameworkCore
```

Linux 上で IBM データ サーバーを操作するには、[IBM.EntityFrameworkCore-lnx NuGet パッケージ](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)をインストールします。

``` powershell
Install-Package IBM.EntityFrameworkCore-lnx
```

## <a name="get-started"></a>開始するには

[IBM .NET Provider for .NET Core の概要](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en)

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* zOS
* IBM dashDB を含む LUW
* IBM I
* Informix

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.6 以降)
* .NET Core
