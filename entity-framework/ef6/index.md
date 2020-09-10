---
title: Entity Framework 6 の概要 - EF6
description: Entity Framework 6 の概要
author: divega
ms.date: 10/23/2016
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
uid: ef6/index
ms.openlocfilehash: 62365a650568525604e82f4363c4e2483a2e7354
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618169"
---
# <a name="entity-framework-6"></a><span data-ttu-id="86a2b-103">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="86a2b-103">Entity Framework 6</span></span>
<span data-ttu-id="86a2b-104">Entity Framework 6 (EF6) は、機能開発および安定化に何年もの時間が費やされてきた、十分に検証された .NET 用のオブジェクト リレーショナル マッパー (O/RM) です。</span><span class="sxs-lookup"><span data-stu-id="86a2b-104">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="86a2b-105">EF6 は、O/RM として、リレーショナルおよびオブジェクト指向の世界のインピーダンス不整合を減らします。開発者はこれにより、アプリケーションのドメインを表す厳密に型指定された .NET オブジェクトを使用した、リレーショナル データベースに格納されたデータをやり取りするアプリケーションを記述でき、通常は記述する必要のあるデータ アクセス "プラミング" コードの大部分を記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="86a2b-105">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="86a2b-106">EF6 には、次のような多数の一般的な O/RM 機能が実装されています。</span><span class="sxs-lookup"><span data-stu-id="86a2b-106">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="86a2b-107">どの EF の種類にも依存しない [POCO](xref:ef6/resources/glossary#poco) エンティティ クラスのマッピング</span><span class="sxs-lookup"><span data-stu-id="86a2b-107">Mapping of [POCO](xref:ef6/resources/glossary#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="86a2b-108">変更の自動追跡</span><span class="sxs-lookup"><span data-stu-id="86a2b-108">Automatic change tracking</span></span>
- <span data-ttu-id="86a2b-109">ID 解決および作業単位</span><span class="sxs-lookup"><span data-stu-id="86a2b-109">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="86a2b-110">Eager、lazy および明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="86a2b-110">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="86a2b-111">[LINQ (言語統合クエリ)](https://aka.ms/AA6hsvu) を使用した厳密に型指定されたクエリの変換</span><span class="sxs-lookup"><span data-stu-id="86a2b-111">Translation of strongly-typed queries using [LINQ (Language INtegrated Query)](https://aka.ms/AA6hsvu)</span></span>
- <span data-ttu-id="86a2b-112">次などのサポートを含む、豊富なマッピング機能があります。</span><span class="sxs-lookup"><span data-stu-id="86a2b-112">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="86a2b-113">一対一、一対多、および多対多のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="86a2b-113">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="86a2b-114">継承 (Table-Per-Hierarchy、Table-Per-Type、Table-Per-Concrete-lass)</span><span class="sxs-lookup"><span data-stu-id="86a2b-114">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="86a2b-115">複合型</span><span class="sxs-lookup"><span data-stu-id="86a2b-115">Complex types</span></span>
  - <span data-ttu-id="86a2b-116">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="86a2b-116">Stored procedures</span></span>
- <span data-ttu-id="86a2b-117">エンティティ モデルを作成するビジュアル デザイナー。</span><span class="sxs-lookup"><span data-stu-id="86a2b-117">A visual designer to create entity models.</span></span>
- <span data-ttu-id="86a2b-118">コードを記述することにより、エンティティ モデルを作成する "Code First" エクスペリエンス。</span><span class="sxs-lookup"><span data-stu-id="86a2b-118">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="86a2b-119">モデルは既存のデータベースから生成し手作業で編集するか、最初から生成して新しいデータベースの作成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="86a2b-119">Models can either be generated from existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="86a2b-120">ASP.NET を含む .NET Framework アプリケーション モデルとの統合および、WPF と WinForms を介したデータ バインド。</span><span class="sxs-lookup"><span data-stu-id="86a2b-120">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="86a2b-121">ADO.NET に基づくデータベース接続、および SQL Server、Oracle、MySQL、SQLite、PostgreSQL、DB2 などへの接続に使用可能な多数の[プロバイダー](xref:ef6/fundamentals/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="86a2b-121">Database connectivity based on ADO.NET and numerous [providers](xref:ef6/fundamentals/providers/index) available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="86a2b-122">EF6 または EF Core のどちらを使用すべきか</span><span class="sxs-lookup"><span data-stu-id="86a2b-122">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="86a2b-123">EF Core は、EF6 と非常によく似た機能およびメリットを持つ、Entity Framework の最新の簡易拡張バージョンです。</span><span class="sxs-lookup"><span data-stu-id="86a2b-123">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="86a2b-124">EF Core は完全に書き直されたもので、EF6 にはない機能が多数含まれています。ただし、EF6 にある最も高度ないくつかのマッピング機能は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="86a2b-124">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="86a2b-125">機能セットが要件に合う場合は、新しいアプリケーション内で EF Core を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="86a2b-125">Consider using EF Core in new applications if the feature set matches your requirements.</span></span>
<span data-ttu-id="86a2b-126">「[EF Core と EF6 を比較する](xref:efcore-and-ef6/index)」ではこの選択についてより詳しく検証しています。</span><span class="sxs-lookup"><span data-stu-id="86a2b-126">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-started"></a>[<span data-ttu-id="86a2b-127">作業の開始</span><span class="sxs-lookup"><span data-stu-id="86a2b-127">Get Started</span></span>](xref:ef6/get-started)

<span data-ttu-id="86a2b-128">EntityFramework NuGet パッケージをプロジェクトに追加するか、[Entity Framework Tools for Visual Studio](https://aka.ms/AA6i8c5) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="86a2b-128">Add the EntityFramework NuGet package to your project or install the [Entity Framework Tools for Visual Studio](https://aka.ms/AA6i8c5).</span></span> <span data-ttu-id="86a2b-129">次いでビデオを見たり、チュートリアルやより高度なドキュメントを読み、EF6 を最大限に活用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="86a2b-129">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="86a2b-130">過去の Entity Framework のバージョン</span><span class="sxs-lookup"><span data-stu-id="86a2b-130">Past Entity Framework Versions</span></span>

<span data-ttu-id="86a2b-131">これは、Entity Framework 6 の最新バージョン用のドキュメントですが、ほとんどが過去のリリースにも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="86a2b-131">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="86a2b-132">EF のすべてのリリース一覧およびそれらによって導入された機能については、「[What's New](xref:ef6/what-is-new/index)」 (新機能) と「[Past Releases](xref:ef6/what-is-new/past-releases)」 (以前のリリース) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86a2b-132">Check out [What's New](xref:ef6/what-is-new/index) and [Past Releases](xref:ef6/what-is-new/past-releases) for a complete list of EF releases and the features they introduced.</span></span>
