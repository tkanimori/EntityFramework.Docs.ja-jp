---
title: Entity Framework Core ツールのリファレンス - EF Core
description: Entity Framework Core CLI ツールと Visual Studio パッケージ マネージャー コンソールのリファレンス ガイド
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635381"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core ツールのリファレンス

Entity Framework Core ツールは、設計時の開発タスクに役立ちます。 主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。

次のツールのいずれかをインストールできます。両方のツールで同じ機能が公開されます。

* [EF Core パッケージ マネージャー コンソール ツール](xref:core/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。 Visual Studio で開発している場合、統合性に優れたこれらのツールの使用をお勧めします。

* [EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。 これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。

## <a name="next-steps"></a>次の手順

* [EF Core パッケージ マネージャー コンソール ツールのリファレンス](xref:core/cli/powershell)
* [EF Core .NET CLI ツールのリファレンス](xref:core/cli/dotnet)
