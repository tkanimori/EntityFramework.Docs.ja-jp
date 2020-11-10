---
title: デザイン時サービス-EF Core
description: Entity Framework Core デザイン時サービスに関する情報
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431313"
---
# <a name="design-time-services"></a><span data-ttu-id="37088-103">デザイン時サービス</span><span class="sxs-lookup"><span data-stu-id="37088-103">Design-time services</span></span>

<span data-ttu-id="37088-104">ツールで使用される一部のサービスは、デザイン時にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="37088-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="37088-105">これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。</span><span class="sxs-lookup"><span data-stu-id="37088-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="37088-106">これらのサービスの1つ (たとえば、移行ファイルを生成するサービス) をオーバーライドするには、の実装を `IDesignTimeServices` スタートアッププロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="37088-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="37088-107">参照 (Microsoft EntityFrameworkCore を)</span><span class="sxs-lookup"><span data-stu-id="37088-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="37088-108">DevelopmentDependency は、パッケージの1つです。</span><span class="sxs-lookup"><span data-stu-id="37088-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="37088-109">これは、依存関係が他のプロジェクトに推移的にフローせず、既定でその型を参照できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="37088-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="37088-110">その型を参照し、デザイン時サービスをオーバーライドするには、プロジェクトファイル内の PackageReference 項目のメタデータを更新します。</span><span class="sxs-lookup"><span data-stu-id="37088-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="37088-111">パッケージが PackageReference を使用して推移的に参照されている場合は、明示的なをパッケージに追加し、そのメタデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37088-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="37088-112">サービスのリスト</span><span class="sxs-lookup"><span data-stu-id="37088-112">List of services</span></span>

<span data-ttu-id="37088-113">デザイン時サービスの一覧を次に示します。</span><span class="sxs-lookup"><span data-stu-id="37088-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="37088-114">サービス</span><span class="sxs-lookup"><span data-stu-id="37088-114">Service</span></span>                                                                              | <span data-ttu-id="37088-115">説明</span><span class="sxs-lookup"><span data-stu-id="37088-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="37088-116">対応するモデル注釈のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="37088-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="37088-117">C# コードの生成に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="37088-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="37088-118">複数化と単数化の単語。</span><span class="sxs-lookup"><span data-stu-id="37088-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="37088-119">移行のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="37088-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="37088-120">移行ファイルを管理するためのメインクラス。</span><span class="sxs-lookup"><span data-stu-id="37088-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="37088-121">データベースからデータベースモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="37088-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="37088-122">モデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="37088-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="37088-123">コードの構成を生成します。</span><span class="sxs-lookup"><span data-stu-id="37088-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="37088-124">リバースエンジニアリングされたモデルをスキャフォールディングするためのメインクラス。</span><span class="sxs-lookup"><span data-stu-id="37088-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="37088-125">サービスの使用</span><span class="sxs-lookup"><span data-stu-id="37088-125">Using services</span></span>

<span data-ttu-id="37088-126">これらのサービスは、独自のツールを作成する場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="37088-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="37088-127">たとえば、デザイン時ワークフローの一部を自動化する場合などです。</span><span class="sxs-lookup"><span data-stu-id="37088-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="37088-128">AddEntityFrameworkDesignTimeServices および AddDbContextDesignTimeServices 拡張メソッドを使用して、これらのサービスを含むサービスプロバイダーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="37088-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
