---
title: Entity Framework Core ツールのリファレンス - EF Core
description: Entity Framework Core CLI ツールと Visual Studio パッケージ マネージャー コンソールのリファレンス ガイド
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431135"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core ツールのリファレンス

Entity Framework Core ツールは、設計時の開発タスクに役立ちます。 主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。

* [EF Core パッケージ マネージャー コンソール ツール](xref:core/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。

* [EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

いずれのツールも機能は同じです。 Visual Studio で開発を行っている場合は、統合性に優れた **パッケージ マネージャー コンソール** ツールの使用をお勧めします。

## <a name="next-steps"></a>次の手順

* [EF Core パッケージ マネージャー コンソール ツールのリファレンス](xref:core/cli/powershell)
* [EF Core .NET CLI ツールのリファレンス](xref:core/cli/dotnet)
