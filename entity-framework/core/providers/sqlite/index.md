---
title: SQLite データベース プロバイダー - EF Core
description: Entity Framework Core SQLite データベース プロバイダーに関する情報
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: ba537acdf537fa475378c08b8c6290930b29239d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071239"
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core データベース プロバイダー

このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* SQLite (3.7 以降)

## <a name="limitations"></a>制限事項

SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](xref:core/providers/sqlite/limitations)をご覧ください。
