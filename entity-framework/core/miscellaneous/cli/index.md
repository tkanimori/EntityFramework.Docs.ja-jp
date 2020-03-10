---
title: Entity Framework Core ツールのリファレンス - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 237192c55ea3542521a7a292ac8550d72e4ef82c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412817"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="4dbc2-102">Entity Framework Core ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4dbc2-102">Entity Framework Core tools reference</span></span>

<span data-ttu-id="4dbc2-103">Entity Framework Core ツールは、設計時の開発タスクに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-103">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="4dbc2-104">主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-104">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="4dbc2-105">[EF Core パッケージ マネージャー コンソール ツール](powershell.md)は、Visual Studio の[パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/tools/package-manager-console)で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-105">The [EF Core Package Manager Console tools](powershell.md) run in the [Package Manager Console](https://docs.microsoft.com/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="4dbc2-106">[EF Core .NET コマンド ライン インターフェイス (CLI) ツール](dotnet.md)は、クロス プラットフォームの [.NET Core CLI ツール](https://docs.microsoft.com/dotnet/core/tools/)の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-106">The [EF Core .NET command-line interface (CLI) tools](dotnet.md) are an extension to the cross-platform [.NET Core CLI tools](https://docs.microsoft.com/dotnet/core/tools/).</span></span> <span data-ttu-id="4dbc2-107">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-107">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="4dbc2-108">いずれのツールも機能は同じです。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-108">Both tools expose the same functionality.</span></span> <span data-ttu-id="4dbc2-109">Visual Studio で開発を行っている場合は、統合性に優れた**パッケージ マネージャー コンソール** ツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4dbc2-109">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4dbc2-110">次の手順</span><span class="sxs-lookup"><span data-stu-id="4dbc2-110">Next steps</span></span>

* [<span data-ttu-id="4dbc2-111">EF Core パッケージ マネージャー コンソール ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4dbc2-111">EF Core Package Manager Console tools reference</span></span>](powershell.md)
* [<span data-ttu-id="4dbc2-112">EF Core .NET CLI ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4dbc2-112">EF Core .NET CLI tools reference</span></span>](dotnet.md)
