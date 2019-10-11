---
title: 空間型のプロバイダーサポート-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 863f1b4551bd62160915eba90fee7ba6c49c169c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181590"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="44fd0-102">空間型のプロバイダーサポート</span><span class="sxs-lookup"><span data-stu-id="44fd0-102">Provider Support for Spatial Types</span></span>
<span data-ttu-id="44fd0-103">Entity Framework は、DbGeography クラスまたは Dbgeography クラスを使用した空間データの操作をサポートします。</span><span class="sxs-lookup"><span data-stu-id="44fd0-103">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="44fd0-104">これらのクラスは、Entity Framework プロバイダーによって提供されるデータベース固有の機能に依存しています。</span><span class="sxs-lookup"><span data-stu-id="44fd0-104">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="44fd0-105">すべてのプロバイダーが空間データをサポートするわけではありません。また、空間型アセンブリのインストールなどの追加の前提条件を持つプロバイダーもあります。</span><span class="sxs-lookup"><span data-stu-id="44fd0-105">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="44fd0-106">サポートされている空間型のプロバイダーの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44fd0-106">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="44fd0-107">アプリケーションでの空間型の使用方法に関する追加情報については、次の2つのチュートリアルを参照してください。1つは Code First 用、もう1つは Database First または Model First です。</span><span class="sxs-lookup"><span data-stu-id="44fd0-107">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="44fd0-108">Code First の空間データ型</span><span class="sxs-lookup"><span data-stu-id="44fd0-108">Spatial Data Types in Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)  
- [<span data-ttu-id="44fd0-109">EF デザイナーの空間データ型</span><span class="sxs-lookup"><span data-stu-id="44fd0-109">Spatial Data Types in EF Designer</span></span>](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="44fd0-110">空間型をサポートする EF のリリース</span><span class="sxs-lookup"><span data-stu-id="44fd0-110">EF releases that support spatial types</span></span>  

<span data-ttu-id="44fd0-111">空間型のサポートは、EF5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="44fd0-111">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="44fd0-112">ただし、EF5 空間型は、アプリケーションが .NET 4.5 を対象として実行されている場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="44fd0-112">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="44fd0-113">EF6 空間型以降では、.NET 4 と .NET 4.5 の両方を対象とするアプリケーションでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="44fd0-113">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="44fd0-114">空間型をサポートする EF プロバイダー</span><span class="sxs-lookup"><span data-stu-id="44fd0-114">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="44fd0-115">EF5</span><span class="sxs-lookup"><span data-stu-id="44fd0-115">EF5</span></span>  

<span data-ttu-id="44fd0-116">空間型をサポートしていることを認識している EF5 の Entity Framework プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="44fd0-116">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="44fd0-117">Microsoft SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="44fd0-117">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="44fd0-118">このプロバイダーは、EF5 の一部として出荷されています。</span><span class="sxs-lookup"><span data-stu-id="44fd0-118">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="44fd0-119">このプロバイダーは、インストールする必要があるその他の下位レベルのライブラリに依存しています。詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44fd0-119">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="44fd0-120">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="44fd0-120">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="44fd0-121">これは Devart のサードパーティプロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="44fd0-121">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="44fd0-122">空間型をサポートする EF5 プロバイダーがわかっている場合は、連絡先を取得してください。この一覧に追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="44fd0-122">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="44fd0-123">EF6</span><span class="sxs-lookup"><span data-stu-id="44fd0-123">EF6</span></span>  

<span data-ttu-id="44fd0-124">空間型をサポートしていることを認識している EF6 の Entity Framework プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="44fd0-124">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="44fd0-125">Microsoft SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="44fd0-125">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="44fd0-126">このプロバイダーは、EF6 の一部として出荷されています。</span><span class="sxs-lookup"><span data-stu-id="44fd0-126">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="44fd0-127">このプロバイダーは、インストールする必要があるその他の下位レベルのライブラリに依存しています。詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44fd0-127">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="44fd0-128">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="44fd0-128">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="44fd0-129">これは Devart のサードパーティプロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="44fd0-129">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="44fd0-130">空間型をサポートする EF6 プロバイダーがわかっている場合は、連絡先を取得してください。この一覧に追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="44fd0-130">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="44fd0-131">Microsoft SQL Server を使用した空間型の前提条件</span><span class="sxs-lookup"><span data-stu-id="44fd0-131">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="44fd0-132">SQL Server 空間サポートは、下位レベルの SQL Server 固有の型 SqlGeography および Sqlgeography に依存します。</span><span class="sxs-lookup"><span data-stu-id="44fd0-132">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="44fd0-133">これらの型は、Microsoft の SqlServer. .dll アセンブリに存在します。このアセンブリは、EF の一部として、または .NET Framework の一部として出荷されることはありません。</span><span class="sxs-lookup"><span data-stu-id="44fd0-133">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="44fd0-134">Visual Studio をインストールすると、多くの場合、SQL Server のバージョンもインストールされます。これには、Microsoft の SqlServer. .dll のインストールが含まれます。</span><span class="sxs-lookup"><span data-stu-id="44fd0-134">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="44fd0-135">空間型を使用するコンピューターに SQL Server がインストールされていない場合、または空間型が SQL Server のインストールから除外されている場合は、手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="44fd0-135">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="44fd0-136">これらの型は、Microsoft SQL Server Feature Pack の一部である @no__t 0 を使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="44fd0-136">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="44fd0-137">空間の種類は SQL Server バージョンによって異なります。そのため、Microsoft ダウンロードセンターで["SQL Server Feature Pack" を検索](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack)し、使用する SQL Server のバージョンに対応するオプションを選択してダウンロードすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="44fd0-137">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
