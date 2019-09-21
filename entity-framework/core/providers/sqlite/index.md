---
title: SQLite データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e4cbdba46f901831892192a343db2920a5760042
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149265"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="871c0-102">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="871c0-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="871c0-103">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="871c0-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="871c0-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="871c0-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="871c0-105">インストール</span><span class="sxs-lookup"><span data-stu-id="871c0-105">Install</span></span>

<span data-ttu-id="871c0-106">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="871c0-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="supported-database-engines"></a><span data-ttu-id="871c0-107">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="871c0-107">Supported Database Engines</span></span>

* <span data-ttu-id="871c0-108">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="871c0-108">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="871c0-109">制限事項</span><span class="sxs-lookup"><span data-stu-id="871c0-109">Limitations</span></span>

<span data-ttu-id="871c0-110">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="871c0-110">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
