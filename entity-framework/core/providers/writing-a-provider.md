---
title: データベースプロバイダーの作成-EF Core
description: 新しい Entity Framework Core プロバイダーの作成に関する情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: e66c5b94d826e35bb5148d57897a1081de4e9736
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128421"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="cce70-103">データベース プロバイダーを記述する</span><span class="sxs-lookup"><span data-stu-id="cce70-103">Writing a Database Provider</span></span>

<span data-ttu-id="cce70-104">Entity Framework Core データベースプロバイダーの作成の詳細については、「 [Arthur ヴィッカース](https://github.com/ajcvickers)」を参照して[EF Core プロバイダーを作成](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cce70-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="cce70-105">これらの投稿は EF Core 1.1 以降に更新されていないため、その時点以降に大幅な変更が加えられています。</span><span class="sxs-lookup"><span data-stu-id="cce70-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>
<span data-ttu-id="cce70-106">[問題 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) は、このドキュメントの更新を追跡しています。</span><span class="sxs-lookup"><span data-stu-id="cce70-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="cce70-107">EF Core codebase はオープンソースであり、参照として使用できるデータベースプロバイダーがいくつか含まれています。</span><span class="sxs-lookup"><span data-stu-id="cce70-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="cce70-108">ソースコードについては、「」を参照して <https://github.com/dotnet/efcore> ください。</span><span class="sxs-lookup"><span data-stu-id="cce70-108">You can find the source code at <https://github.com/dotnet/efcore>.</span></span> <span data-ttu-id="cce70-109">また、 [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)、 [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)など、一般的に使用されるサードパーティプロバイダーのコードを確認すると役立つ場合もあります。</span><span class="sxs-lookup"><span data-stu-id="cce70-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="cce70-110">特に、これらのプロジェクトは、NuGet で公開する機能テストから拡張して実行するように設定されています。</span><span class="sxs-lookup"><span data-stu-id="cce70-110">In particular, these projects are set up to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="cce70-111">この種のセットアップは強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cce70-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="cce70-112">プロバイダーの変更を最新の状態に保つ</span><span class="sxs-lookup"><span data-stu-id="cce70-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="cce70-113">2.1 リリース後の作業では、プロバイダーコードの対応する変更が必要になる可能性 [のある変更のログ](xref:core/providers/provider-log) を作成しました。</span><span class="sxs-lookup"><span data-stu-id="cce70-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="cce70-114">これは、既存のプロバイダーを更新して新しいバージョンの EF Core で動作する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cce70-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="cce70-115">2.1 より前では、 [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) GitHub の [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) 問題とプル要求について、とのラベルを同様の目的で使用していました。</span><span class="sxs-lookup"><span data-stu-id="cce70-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) and [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="cce70-116">これらの問題に対してこれらの continiue を使用して、特定のリリースのどの作業項目についても、プロバイダーでの作業が必要になる可能性があることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="cce70-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="cce70-117">ラベルは、 `providers-beware` 通常、作業項目の実装によってプロバイダーが壊れる可能性があることを意味しますが、 `providers-fyi` ラベルは通常、プロバイダーが破損しないことを意味しますが、新しい機能を有効にするなど、コードを変更する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="cce70-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="cce70-118">サードパーティプロバイダーの推奨される名前付け</span><span class="sxs-lookup"><span data-stu-id="cce70-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="cce70-119">NuGet パッケージには、次の名前付けを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cce70-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="cce70-120">これは、EF Core チームによって配信されるパッケージの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="cce70-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="cce70-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cce70-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
