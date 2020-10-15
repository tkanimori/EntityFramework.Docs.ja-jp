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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="223bc-103">Entity Framework Core ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="223bc-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="223bc-104">Entity Framework Core ツールは、設計時の開発タスクに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="223bc-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="223bc-105">主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="223bc-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="223bc-106">[EF Core パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。</span><span class="sxs-lookup"><span data-stu-id="223bc-106">The [EF Core Package Manager Console tools](xref:core/miscellaneous/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="223bc-107">[EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/miscellaneous/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="223bc-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="223bc-108">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="223bc-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="223bc-109">いずれのツールも機能は同じです。</span><span class="sxs-lookup"><span data-stu-id="223bc-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="223bc-110">Visual Studio で開発を行っている場合は、統合性に優れた**パッケージ マネージャー コンソール** ツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="223bc-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="223bc-111">次の手順</span><span class="sxs-lookup"><span data-stu-id="223bc-111">Next steps</span></span>

* [<span data-ttu-id="223bc-112">EF Core パッケージ マネージャー コンソール ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="223bc-112">EF Core Package Manager Console tools reference</span></span>](xref:core/miscellaneous/cli/powershell)
* [<span data-ttu-id="223bc-113">EF Core .NET CLI ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="223bc-113">EF Core .NET CLI tools reference</span></span>](xref:core/miscellaneous/cli/dotnet)
