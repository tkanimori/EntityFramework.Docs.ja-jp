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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="4d6f1-103">Entity Framework Core ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4d6f1-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="4d6f1-104">Entity Framework Core ツールは、設計時の開発タスクに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="4d6f1-105">主に移行の管理と、`DbContext` およびエンティティ型のスキャフォールディング (データベースのスキーマをリバース エンジニアリングする) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="4d6f1-106">[EF Core パッケージ マネージャー コンソール ツール](xref:core/cli/powershell)は、Visual Studio の[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-106">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="4d6f1-107">[EF Core .NET コマンド ライン インターフェイス (CLI) ツール](xref:core/cli/dotnet)は、クロス プラットフォームの [.NET Core CLI ツール](/dotnet/core/tools/)の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="4d6f1-108">これらのツールには、.NET Core SDK プロジェクトが必要です (プロジェクト ファイルに `Sdk="Microsoft.NET.Sdk"` か同様のものが含まれる)。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="4d6f1-109">いずれのツールも機能は同じです。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="4d6f1-110">Visual Studio で開発を行っている場合は、統合性に優れた **パッケージ マネージャー コンソール** ツールの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4d6f1-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d6f1-111">次の手順</span><span class="sxs-lookup"><span data-stu-id="4d6f1-111">Next steps</span></span>

* [<span data-ttu-id="4d6f1-112">EF Core パッケージ マネージャー コンソール ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4d6f1-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="4d6f1-113">EF Core .NET CLI ツールのリファレンス</span><span class="sxs-lookup"><span data-stu-id="4d6f1-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
