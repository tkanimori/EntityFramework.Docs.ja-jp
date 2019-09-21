---
title: Entity Framework Core ツールのリファレンス - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 237192c55ea3542521a7a292ac8550d72e4ef82c
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149088"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core ツールのリファレンス

Entity Framework Core ツールは、設計時の開発タスクに役立ちます。 主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。

* [EF Core パッケージ マネージャー コンソール ツール](powershell.md)は、Visual Studio の[パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/tools/package-manager-console)で実行されます。

* [EF Core .NET コマンド ライン インターフェイス (CLI) ツール](dotnet.md)は、クロス プラットフォームの [.NET Core CLI ツール](https://docs.microsoft.com/dotnet/core/tools/)の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

いずれのツールも機能は同じです。 Visual Studio で開発を行っている場合は、統合性に優れた**パッケージ マネージャー コンソール** ツールの使用をお勧めします。

## <a name="next-steps"></a>次の手順

* [EF Core パッケージ マネージャー コンソール ツールのリファレンス](powershell.md)
* [EF Core .NET CLI ツールのリファレンス](dotnet.md)
