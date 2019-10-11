---
title: EF Core 1.0 RC2 から RTM-EF Core へのアップグレード
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: e7f121d18931e26e7b5d11842da6da4a9b789efe
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181360"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="af978-102">EF Core 1.0 RC2 から RTM へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="af978-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="af978-103">この記事では、RC2 パッケージでビルドされたアプリケーションを 1.0.0 RTM に移動するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="af978-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="af978-104">パッケージのバージョン</span><span class="sxs-lookup"><span data-stu-id="af978-104">Package Versions</span></span>

<span data-ttu-id="af978-105">アプリケーションに通常インストールする最上位レベルのパッケージの名前は、RC2 と RTM の間で変更されませんでした。</span><span class="sxs-lookup"><span data-stu-id="af978-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="af978-106">**インストールされているパッケージを RTM バージョンにアップグレードする必要があります。**</span><span class="sxs-lookup"><span data-stu-id="af978-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="af978-107">ランタイムパッケージ (たとえば、`Microsoft.EntityFrameworkCore.SqlServer`) が `1.0.0-rc2-final` から `1.0.0` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="af978-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="af978-108">@No__t 0 のパッケージが `1.0.0-preview1-final` から `1.0.0-preview2-final` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="af978-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="af978-109">ツールはまだプレリリースであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="af978-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="af978-110">既存の移行に maxLength を追加する必要がある</span><span class="sxs-lookup"><span data-stu-id="af978-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="af978-111">RC2 では、移行の列の定義は `table.Column<string>(nullable: true)` のように見え、列の長さは、移行の背後にあるコードに格納しているいくつかのメタデータで検索されました。</span><span class="sxs-lookup"><span data-stu-id="af978-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="af978-112">RTM では、この長さはスキャフォールディングコード `table.Column<string>(maxLength: 450, nullable: true)` に含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="af978-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="af978-113">RTM を使用する前にスキャフォールディングされていた既存の移行には、@no__t 0 引数が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="af978-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="af978-114">これは、データベースでサポートされている最大長が使用されることを意味します (SQL Server では `nvarchar(max)`)。</span><span class="sxs-lookup"><span data-stu-id="af978-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="af978-115">これは一部の列に対しては問題ありませんが、キー、外部キー、またはインデックスの一部である列は、最大長を含むように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af978-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="af978-116">慣例により、450はキー、外部キー、およびインデックス付き列に使用される最大長です。</span><span class="sxs-lookup"><span data-stu-id="af978-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="af978-117">モデルで長さが明示的に構成されている場合は、代わりにその長さを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af978-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="af978-118">**ASP.NET Identity**</span><span class="sxs-lookup"><span data-stu-id="af978-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="af978-119">この変更は、ASP.NET Identity を使用し、RTM 以前のプロジェクトテンプレートから作成されたプロジェクトに影響します。</span><span class="sxs-lookup"><span data-stu-id="af978-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="af978-120">プロジェクトテンプレートには、データベースの作成に使用される移行が含まれています。</span><span class="sxs-lookup"><span data-stu-id="af978-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="af978-121">この移行を編集して、次の列に `256` の最大長を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af978-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="af978-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="af978-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="af978-123">名前</span><span class="sxs-lookup"><span data-stu-id="af978-123">Name</span></span>

    * <span data-ttu-id="af978-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="af978-124">NormalizedName</span></span>

*  <span data-ttu-id="af978-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="af978-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="af978-126">Email</span><span class="sxs-lookup"><span data-stu-id="af978-126">Email</span></span>

   * <span data-ttu-id="af978-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="af978-127">NormalizedEmail</span></span>

   * <span data-ttu-id="af978-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="af978-128">NormalizedUserName</span></span>

   * <span data-ttu-id="af978-129">UserName</span><span class="sxs-lookup"><span data-stu-id="af978-129">UserName</span></span>

<span data-ttu-id="af978-130">この変更を行わないと、最初の移行がデータベースに適用されるときに、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="af978-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

```console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="af978-131">.NET Core:Project. json の "インポート" を削除する</span><span class="sxs-lookup"><span data-stu-id="af978-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="af978-132">.NET Core を RC2 でターゲットとしている場合は、.NET Standard をサポートしていない EF Core の依存関係のいくつかについて、一時的な回避策として `imports` をプロジェクトに追加する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="af978-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="af978-133">これらは削除できます。</span><span class="sxs-lookup"><span data-stu-id="af978-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="af978-134">バージョン 1.0 RTM の時点では、 [.NET Core SDK](https://www.microsoft.com/net/download/core)は `project.json` をサポートしなくなりました。また、Visual Studio 2015 を使用して .net Core アプリケーションを開発することもできなくなりました。</span><span class="sxs-lookup"><span data-stu-id="af978-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="af978-135">[project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="af978-135">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="af978-136">Visual Studio を使用している場合は、 [Visual studio 2017](https://www.visualstudio.com/downloads/)にアップグレードすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="af978-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="af978-137">UWPバインドリダイレクトの追加</span><span class="sxs-lookup"><span data-stu-id="af978-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="af978-138">ユニバーサル Windows プラットフォーム (UWP) プロジェクトで EF コマンドを実行しようとすると、次のエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="af978-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```console
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="af978-139">UWP プロジェクトにバインドリダイレクトを手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af978-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="af978-140">プロジェクトルートフォルダーに `App.config` という名前のファイルを作成し、適切なアセンブリバージョンにリダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="af978-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
