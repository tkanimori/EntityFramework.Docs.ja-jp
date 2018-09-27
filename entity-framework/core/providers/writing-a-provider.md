---
title: データベース プロバイダー - EF Core の書き込み
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: c7130b0d104cd26584d298da98eb3e7080ee7f3c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993667"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="9a628-102">データベース プロバイダーの作成</span><span class="sxs-lookup"><span data-stu-id="9a628-102">Writing a Database Provider</span></span>

<span data-ttu-id="9a628-103">Entity Framework Core データベース プロバイダーの作成方法の詳細については、次を参照してください。 [、EF Core プロバイダーを記述するように](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)によって[Arthur Vickers](https://github.com/ajcvickers)します。</span><span class="sxs-lookup"><span data-stu-id="9a628-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="9a628-104">ブログの投稿が EF Core 1.1 以降に更新されていないと、その後大幅な変更も加えられて[問題 681](https://github.com/aspnet/EntityFramework.Docs/issues/681)がこのドキュメントに対する更新プログラムを追跡します。</span><span class="sxs-lookup"><span data-stu-id="9a628-104">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time [Issue 681](https://github.com/aspnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="9a628-105">EF Core のコードベースはオープン ソースであるため、参照として使用できるいくつかのデータベース プロバイダーを含んでいます。</span><span class="sxs-lookup"><span data-stu-id="9a628-105">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="9a628-106">ソース コードを検索することができます https://github.com/aspnet/EntityFrameworkCore します。</span><span class="sxs-lookup"><span data-stu-id="9a628-106">You can find the source code at https://github.com/aspnet/EntityFrameworkCore.</span></span> <span data-ttu-id="9a628-107">サード パーティ プロバイダーの一般的に使用される場合など、コードを検索する便利な場合もあります[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)、および[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)します。</span><span class="sxs-lookup"><span data-stu-id="9a628-107">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="9a628-108">具体的には、これらのプロジェクトは、セットアップから拡張して、NuGet で公開される機能テストを実行します。</span><span class="sxs-lookup"><span data-stu-id="9a628-108">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="9a628-109">この種のセットアップは強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9a628-109">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="9a628-110">プロバイダーの変更で最新に保つ</span><span class="sxs-lookup"><span data-stu-id="9a628-110">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="9a628-111">作成した 2.1 のリリース後、以降の作業を[の変更ログ](provider-log.md)プロバイダ コードに対応する変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a628-111">Starting with work after the 2.1 release, we have created a [log of changes](provider-log.md) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="9a628-112">これは、目的は、新しいバージョンの EF Core を使用する既存のプロバイダーを更新するときにします。</span><span class="sxs-lookup"><span data-stu-id="9a628-112">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="9a628-113">使用して 2.1 では、前に、 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) GitHub の懸案事項と同様の目的のプル要求のラベル。</span><span class="sxs-lookup"><span data-stu-id="9a628-113">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="9a628-114">ここでの問題をこれらのラベルを使用しているかを示す特定のリリースでは、どの作業項目は、プロバイダーで実行する作業を必要がありますを continiue します。</span><span class="sxs-lookup"><span data-stu-id="9a628-114">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="9a628-115">A`providers-beware`ラベル、通常、作業項目の実装が、プロバイダーを分割ことを意味中に、`providers-fyi`プロバイダーは切断されますが、コードは、変更するなどの新機能を有効にする必要があります、通常ラベルを意味します。</span><span class="sxs-lookup"><span data-stu-id="9a628-115">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="9a628-116">サード パーティ プロバイダーの名前付けの推奨</span><span class="sxs-lookup"><span data-stu-id="9a628-116">Suggested naming of third party providers</span></span>

<span data-ttu-id="9a628-117">NuGet パッケージの次の名前付けを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9a628-117">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="9a628-118">これは、EF Core チームによって提供されるパッケージの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="9a628-118">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="9a628-119">例えば:</span><span class="sxs-lookup"><span data-stu-id="9a628-119">For example:</span></span>
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
