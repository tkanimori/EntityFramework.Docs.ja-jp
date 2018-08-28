---
title: InMemory データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: ca802f55d973b9f79073c2507c1e0c7a853a1fce
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993321"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core In-Memory データベース プロバイダー

このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。 これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a>開始するには

このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。
* [InMemory のテスト](../../miscellaneous/testing/in-memory.md)

* [UnicornStore サンプル アプリケーション テスト](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* 組み込みメモリ内データベース (テスト目的専用として設計されています)

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)

* .NET Core

* Mono (4.2.0 以降)

* ユニバーサル Windows プラットフォーム
