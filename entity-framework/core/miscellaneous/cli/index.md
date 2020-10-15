---
title: Entity Framework Core ツールのリファレンス - EF Core
description: Entity Framework Core CLI ツールと Visual Studio パッケージ マネージャー コンソールのリファレンス ガイド
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061997"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core ツールのリファレンス

Entity Framework Core ツールは、設計時の開発タスクに役立ちます。 主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。

* [EF Core パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。

* [EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/miscellaneous/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

いずれのツールも機能は同じです。 Visual Studio で開発を行っている場合は、統合性に優れた**パッケージ マネージャー コンソール** ツールの使用をお勧めします。

## <a name="next-steps"></a>次の手順

* [EF Core パッケージ マネージャー コンソール ツールのリファレンス](xref:core/miscellaneous/cli/powershell)
* [EF Core .NET CLI ツールのリファレンス](xref:core/miscellaneous/cli/dotnet)
