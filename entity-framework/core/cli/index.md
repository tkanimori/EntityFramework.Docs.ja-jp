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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="d7388-103">Entity Framework Core ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="d7388-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="d7388-104">Entity Framework Core ツールは、設計時の開発タスクに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d7388-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="d7388-105">主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d7388-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

<span data-ttu-id="d7388-106">次のツールのいずれかをインストールできます。両方のツールで同じ機能が公開されます。</span><span class="sxs-lookup"><span data-stu-id="d7388-106">Either of the following tools can be installed, as both tools expose the same functionality:</span></span>

* <span data-ttu-id="d7388-107">[EF Core パッケージ マネージャー コンソール ツール](xref:core/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。</span><span class="sxs-lookup"><span data-stu-id="d7388-107">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span> <span data-ttu-id="d7388-108">Visual Studio で開発している場合、統合性に優れたこれらのツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d7388-108">We recommend using these tools if you are developing in Visual Studio as they provide a more integrated experience.</span></span>

* <span data-ttu-id="d7388-109">[EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="d7388-109">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="d7388-110">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="d7388-110">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7388-111">次の手順</span><span class="sxs-lookup"><span data-stu-id="d7388-111">Next steps</span></span>

* [<span data-ttu-id="d7388-112">EF Core パッケージ マネージャー コンソール ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="d7388-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="d7388-113">EF Core .NET CLI ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="d7388-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
