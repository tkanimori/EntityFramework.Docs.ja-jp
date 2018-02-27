---
title: "PostgreSQL の Npgsql データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 5ecd1b22-c68e-4d87-ba39-b0761f4d5b90
ms.technology: entity-framework-core
uid: core/providers/npgsql/index
ms.openlocfilehash: acf2e18d7a608b0d75b571a5ac0199d84f86066b
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="npgsql-ef-core-database-provider-for-postgresql"></a>PostgreSQL の Npgsql EF Core データベース プロバイダー

このデータベース プロバイダーにより、PostgreSQL と共に Entity Framework Core を使用できます。 このプロバイダーは [Npgsql プロジェクト](http://www.npgsql.org)の一部として保守管理されます。

> [!NOTE]  
> このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。

## <a name="install"></a>Install

[Npgsql.EntityFrameworkCore.PostgreSQL NuGet パッケージ](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)をインストールします。

``` powershell
Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
```

## <a name="get-started"></a>開始するには

開始するにあたり、[Npgsql ドキュメント](http://www.npgsql.org/efcore/index.html)を参照してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* PostgreSQL

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)

* .NET Core

* Mono (4.2.0 以降)
