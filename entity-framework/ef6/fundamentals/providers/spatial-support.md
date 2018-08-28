---
title: 空間型 - EF6 のプロバイダーのサポート
author: divega
ms.date: 2016-10-23
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 07eeecb5f5e3e3eab8548c4c7c0ed55c5ffb4f31
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998288"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="8fd4d-102">空間型のプロバイダー サポート</span><span class="sxs-lookup"><span data-stu-id="8fd4d-102">Provider Support for Spatial Types</span></span>
<span data-ttu-id="8fd4d-103">Entity Framework では、DbGeography または DbGeometry クラスを使用して空間データの操作をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-103">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="8fd4d-104">これらのクラスは、Entity Framework プロバイダーによって提供されるデータベースに固有の機能に依存します。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-104">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="8fd4d-105">空間データをサポートしていないすべてのプロバイダーと、空間型のアセンブリのインストールなどの追加の前提条件はいる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-105">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="8fd4d-106">空間型のプロバイダー サポートの詳細については、次に示します。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-106">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="8fd4d-107">アプリケーションで空間型を使用する方法の詳細については、Database First または Model First をその他の Code First での 1 つ、2 つのチュートリアルではあります。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-107">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="8fd4d-108">空間データ型は、最初のコード</span><span class="sxs-lookup"><span data-stu-id="8fd4d-108">Spatial Data Types in Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)  
- [<span data-ttu-id="8fd4d-109">EF デザイナーで空間データ型</span><span class="sxs-lookup"><span data-stu-id="8fd4d-109">Spatial Data Types in EF Designer</span></span>](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="8fd4d-110">空間型をサポートする EF のリリース</span><span class="sxs-lookup"><span data-stu-id="8fd4d-110">EF releases that support spatial types</span></span>  

<span data-ttu-id="8fd4d-111">空間型のサポートは、EF5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-111">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="8fd4d-112">ただし、EF5 で空間型は場合にのみアプリケーションを対象とし、.NET 4.5 で実行されます。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-112">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="8fd4d-113">EF6 の空間型は .NET 4 および .NET 4.5 の両方を対象とするアプリケーションのサポートを開始しています。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-113">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="8fd4d-114">空間型をサポートする EF プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8fd4d-114">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="8fd4d-115">EF5</span><span class="sxs-lookup"><span data-stu-id="8fd4d-115">EF5</span></span>  

<span data-ttu-id="8fd4d-116">空間型のサポートがの認識は EF5 の Entity Framework プロバイダー:</span><span class="sxs-lookup"><span data-stu-id="8fd4d-116">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="8fd4d-117">Microsoft SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8fd4d-117">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="8fd4d-118">このプロバイダーは EF5 の一部として出荷されます。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-118">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="8fd4d-119">このプロバイダーによって異なりますいくつか追加の低レベル ライブラリをインストールする必要があります-詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-119">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="8fd4d-120">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="8fd4d-120">Devart dotConnect for Oracle</span></span>](http://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="8fd4d-121">Devart からサード パーティ プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-121">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="8fd4d-122">わかっている場合してください、空間型をサポートする EF5 プロバイダーの連絡先を取得し、この一覧に追加することをお知らせいたします。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-122">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="8fd4d-123">EF6</span><span class="sxs-lookup"><span data-stu-id="8fd4d-123">EF6</span></span>  

<span data-ttu-id="8fd4d-124">空間型のサポートがの認識が EF6 の Entity Framework プロバイダー:</span><span class="sxs-lookup"><span data-stu-id="8fd4d-124">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="8fd4d-125">Microsoft SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8fd4d-125">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="8fd4d-126">このプロバイダーは、EF6 の一部として出荷されます。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-126">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="8fd4d-127">このプロバイダーによって異なりますいくつか追加の低レベル ライブラリをインストールする必要があります-詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-127">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="8fd4d-128">Devart dotConnect for Oracle</span><span class="sxs-lookup"><span data-stu-id="8fd4d-128">Devart dotConnect for Oracle</span></span>](http://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="8fd4d-129">Devart からサード パーティ プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-129">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="8fd4d-130">わかっている場合してください、空間型をサポートする EF6 プロバイダーの連絡先を取得し、この一覧に追加することをお知らせいたします。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-130">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="8fd4d-131">Microsoft SQL Server での空間型の前提条件</span><span class="sxs-lookup"><span data-stu-id="8fd4d-131">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="8fd4d-132">SQL Server 空間のサポートは、低レベルの SQL Server に固有の型を SqlGeography および SqlGeometry に依存します。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-132">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="8fd4d-133">アセンブリ Microsoft.SqlServer.Types.dll でこれらの型が存在し、EF の一部として、または .NET Framework の一部として、このアセンブリが付属していません。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-133">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="8fd4d-134">Visual Studio がインストールされている場合、SQL Server のバージョンも多くの場合、インストールされ、これは、Microsoft.SqlServer.Types.dll のインストールが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-134">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="8fd4d-135">空間型を使用するコンピューターの SQL Server がインストールされていない場合、または空間型は、SQL Server のインストールから除外された場合は、それらを手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-135">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="8fd4d-136">使用して、種類をインストールすることができます`SQLSysClrTypes.msi`、Microsoft SQL Server Feature Pack の一部です。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-136">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="8fd4d-137">空間型は、バージョン固有の SQL Server をお勧め["SQL Server Feature Pack"を検索](https://www.microsoft.com/en-us/search/result.aspx?q=sql+server+feature+pack)Microsoft ダウンロード センターを選択し、使用する SQL Server のバージョンに対応するオプションをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="8fd4d-137">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/en-us/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>
