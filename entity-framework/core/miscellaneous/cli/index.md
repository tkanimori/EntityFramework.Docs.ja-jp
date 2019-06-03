---
title: Entity Framework Core ツールのリファレンス - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 13e80f740bc5ce3404e8dba40b65ec872c5e3f90
ms.sourcegitcommit: ea1cdec0b982b922a59b9d9301d3ed2b94baca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66452261"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core ツールのリファレンス

Entity Framework Core ツールは、設計時の開発タスクに役立ちます。 主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。

* [EF Core パッケージ マネージャー コンソール ツール](powershell.md)は、Visual Studio の[パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/tools/package-manager-console)で実行されます。 これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。

* [EF Core .NET コマンド ライン インターフェイス (CLI) ツール](dotnet.md)は、クロス プラットフォームの [.NET Core CLI ツール](https://docs.microsoft.com/dotnet/core/tools/)の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

いずれのツールも機能は同じです。 Visual Studio で開発を行っている場合は、統合性に優れた**パッケージ マネージャー コンソール** ツールの使用をお勧めします。

## <a name="next-steps"></a>次の手順

* [EF Core パッケージ マネージャー コンソール ツールのリファレンス](powershell.md)
* [EF Core .NET CLI ツールのリファレンス](dotnet.md)
