---
title: 概要 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
caps.latest.revision: 5
uid: ef6/index
ms.openlocfilehash: c74626593c4109badd75f2a0a4f051c840f7bc7f
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614380"
---
# <a name="entity-framework-6"></a><span data-ttu-id="8c2d9-102">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="8c2d9-102">Entity Framework 6</span></span>
<span data-ttu-id="8c2d9-103">Entity Framework 6 (EF6) は、機能開発および安定化に何年もの時間が費やされてきた、十分に検証された .NET 用のオブジェクト リレーショナル マッパー (O/RM) です。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-103">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="8c2d9-104">EF6 は、O/RM として、リレーショナルおよびオブジェクト指向の世界のインピーダンス不整合を減らします。開発者はこれにより、アプリケーションのドメインを表す厳密に型指定された .NET オブジェクトを使用した、リレーショナル データベースに格納されたデータをやり取りするアプリケーションを記述でき、通常は記述する必要のあるデータ アクセス "プラミング" コードの大部分を記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-104">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="8c2d9-105">EF6 には、次のような多数の一般的な O/RM 機能が実装されています。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-105">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="8c2d9-106">どの EF の種類にも依存しない [POCO](~/ef6/resources/glossary.md#poco) エンティティ クラスのマッピング</span><span class="sxs-lookup"><span data-stu-id="8c2d9-106">Mapping of [POCO](~/ef6/resources/glossary.md#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="8c2d9-107">変更の自動追跡</span><span class="sxs-lookup"><span data-stu-id="8c2d9-107">Automatic change tracking</span></span>
- <span data-ttu-id="8c2d9-108">ID 解決および作業単位</span><span class="sxs-lookup"><span data-stu-id="8c2d9-108">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="8c2d9-109">Eager、lazy および明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="8c2d9-109">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="8c2d9-110">LINQ (言語統合クエリ) を使用した厳密に型指定されたクエリの変換</span><span class="sxs-lookup"><span data-stu-id="8c2d9-110">Translation of strongly-typed queries using LINQ (Language INtegrated Query)</span></span>
- <span data-ttu-id="8c2d9-111">次などのサポートを含む、豊富なマッピング機能があります。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-111">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="8c2d9-112">一対一、一対多、および多対多のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="8c2d9-112">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="8c2d9-113">継承 (Table-Per-Hierarchy、Table-Per-Type、Table-Per-Concrete-lass)</span><span class="sxs-lookup"><span data-stu-id="8c2d9-113">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="8c2d9-114">複合型</span><span class="sxs-lookup"><span data-stu-id="8c2d9-114">Complex types</span></span>
  - <span data-ttu-id="8c2d9-115">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="8c2d9-115">Stored procedures</span></span>
- <span data-ttu-id="8c2d9-116">エンティティ モデルを作成するビジュアル デザイナー。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-116">A visual designer to create entity models.</span></span>
- <span data-ttu-id="8c2d9-117">コードを記述することにより、エンティティ モデルを作成する "Code First" エクスペリエンス。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-117">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="8c2d9-118">モデルは既存のデータベースから生成し手作業で編集するか、最初から生成して新しいデータベースの作成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-118">Models can either be generated form existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="8c2d9-119">ASP.NET を含む .NET Framework アプリケーション モデルとの統合および、WPF と WinForms を介したデータ バインド。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-119">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="8c2d9-120">ADO.NET に基づくデータベース接続、および SQL Server、Oracle、MySQL、SQLite、PostgreSQL、DB2 などへの接続に使用可能な多数のプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-120">Database connectivity based on ADO.NET and numerous providers available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="8c2d9-121">EF6 または EF Core のどちらを使用すべきか</span><span class="sxs-lookup"><span data-stu-id="8c2d9-121">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="8c2d9-122">EF Core は、EF6 と非常によく似た機能およびメリットを持つ、Entity Framework の最新の簡易拡張バージョンです。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-122">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="8c2d9-123">EF Core は完全に書き直されたもので、EF6 にはない機能が多数含まれています。ただし、EF6 にある最も高度ないくつかのマッピング機能は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-123">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="8c2d9-124">機能セットが要件に合う場合、新しいアプリケーションでは EF Core を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-124">We recommend using EF Core in new applications as long as the feature set matches your requirements.</span></span>
<span data-ttu-id="8c2d9-125">「[EF Core と EF6 を比較する](xref:efcore-and-ef6/index)」ではこの選択についてより詳しく検証しています。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-125">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-startedef6get-startedmd"></a>[<span data-ttu-id="8c2d9-126">開始するには</span><span class="sxs-lookup"><span data-stu-id="8c2d9-126">Get Started</span></span>](~/ef6/get-started.md)

<span data-ttu-id="8c2d9-127">EntityFramework NuGet パッケージをプロジェクトに追加するか、Entity Framework Tools for Visual Studio をインストールします。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-127">Add the EntityFramework NuGet package to your project or install the Entity Framework Tools for Visual Studio.</span></span> <span data-ttu-id="8c2d9-128">次いでビデオを見たり、チュートリアルやより高度なドキュメントを読み、EF6 を最大限に活用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-128">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="8c2d9-129">過去の Entity Framework のバージョン</span><span class="sxs-lookup"><span data-stu-id="8c2d9-129">Past Entity Framework Versions</span></span>

<span data-ttu-id="8c2d9-130">これは、Entity Framework 6 の最新バージョン用のドキュメントですが、ほとんどが過去のリリースにも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-130">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="8c2d9-131">EF のすべてのリリース一覧およびそれらによって導入された機能については、「[What's New](~/ef6/what-is-new/index.md)」 (新機能) と「[Past Releases](~/ef6/what-is-new/past-releases.md)」 (以前のリリース) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2d9-131">Check out [What's New](~/ef6/what-is-new/index.md) and [Past Releases](~/ef6/what-is-new/past-releases.md) for a complete list of EF releases and the features they introduced.</span></span>
