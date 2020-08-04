---
title: EF Core 1.0 RC2 から RTM-EF Core へのアップグレード
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526772"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="37466-102">EF Core 1.0 RC2 から RTM へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="37466-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="37466-103">この記事では、RC2 パッケージでビルドされたアプリケーションを 1.0.0 RTM に移動するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="37466-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="37466-104">パッケージのバージョン</span><span class="sxs-lookup"><span data-stu-id="37466-104">Package Versions</span></span>

<span data-ttu-id="37466-105">アプリケーションに通常インストールする最上位レベルのパッケージの名前は、RC2 と RTM の間で変更されませんでした。</span><span class="sxs-lookup"><span data-stu-id="37466-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="37466-106">**インストールされているパッケージを RTM バージョンにアップグレードする必要があります。**</span><span class="sxs-lookup"><span data-stu-id="37466-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="37466-107">ランタイムパッケージ (たとえば、 `Microsoft.EntityFrameworkCore.SqlServer` ) はからに変更されました `1.0.0-rc2-final` `1.0.0` 。</span><span class="sxs-lookup"><span data-stu-id="37466-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="37466-108">`Microsoft.EntityFrameworkCore.Tools`パッケージがからに変更されました `1.0.0-preview1-final` `1.0.0-preview2-final` 。</span><span class="sxs-lookup"><span data-stu-id="37466-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="37466-109">ツールはまだプレリリースであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="37466-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="37466-110">既存の移行に maxLength を追加する必要がある</span><span class="sxs-lookup"><span data-stu-id="37466-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="37466-111">RC2 では、移行の列の定義は、 `table.Column<string>(nullable: true)` 移行の背後にあるコードに格納しているいくつかのメタデータで検索されています。</span><span class="sxs-lookup"><span data-stu-id="37466-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="37466-112">RTM では、スキャフォールディングコードに長さが含まれるようになりました `table.Column<string>(maxLength: 450, nullable: true)` 。</span><span class="sxs-lookup"><span data-stu-id="37466-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="37466-113">RTM を使用する前にスキャフォールディングしていた既存の移行には、引数が指定されていません `maxLength` 。</span><span class="sxs-lookup"><span data-stu-id="37466-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="37466-114">これは、データベースでサポートされている最大長 (SQL Server) が使用されることを意味し `nvarchar(max)` ます。</span><span class="sxs-lookup"><span data-stu-id="37466-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="37466-115">これは一部の列に対しては問題ありませんが、キー、外部キー、またはインデックスの一部である列は、最大長を含むように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37466-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="37466-116">慣例により、450はキー、外部キー、およびインデックス付き列に使用される最大長です。</span><span class="sxs-lookup"><span data-stu-id="37466-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="37466-117">モデルで長さが明示的に構成されている場合は、代わりにその長さを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37466-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="37466-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="37466-118">ASP.NET Identity</span></span>

<span data-ttu-id="37466-119">この変更は、ASP.NET Identity を使用し、RTM 以前のプロジェクトテンプレートから作成されたプロジェクトに影響します。</span><span class="sxs-lookup"><span data-stu-id="37466-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="37466-120">プロジェクトテンプレートには、データベースの作成に使用される移行が含まれています。</span><span class="sxs-lookup"><span data-stu-id="37466-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="37466-121">この移行を編集して、次の列の最大長を指定する必要があり `256` ます。</span><span class="sxs-lookup"><span data-stu-id="37466-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="37466-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="37466-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="37466-123">名前</span><span class="sxs-lookup"><span data-stu-id="37466-123">Name</span></span>
  * <span data-ttu-id="37466-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="37466-124">NormalizedName</span></span>
* <span data-ttu-id="37466-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="37466-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="37466-126">電子メール</span><span class="sxs-lookup"><span data-stu-id="37466-126">Email</span></span>
  * <span data-ttu-id="37466-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="37466-127">NormalizedEmail</span></span>
  * <span data-ttu-id="37466-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="37466-128">NormalizedUserName</span></span>
  * <span data-ttu-id="37466-129">UserName</span><span class="sxs-lookup"><span data-stu-id="37466-129">UserName</span></span>

<span data-ttu-id="37466-130">この変更を行わないと、最初の移行がデータベースに適用されるときに、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="37466-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="37466-131">.NET Core: project.jsの [インポート] を削除します。</span><span class="sxs-lookup"><span data-stu-id="37466-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="37466-132">.NET Core を RC2 で対象としていた場合は、 `imports` .NET Standard をサポートしていない EF Core の依存関係の一部に対する一時的な回避策として project.jsにを追加する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="37466-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="37466-133">これらは削除できます。</span><span class="sxs-lookup"><span data-stu-id="37466-133">These can now be removed.</span></span>

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
> <span data-ttu-id="37466-134">バージョン 1.0 RTM の時点では、 [.NET Core SDK](https://www.microsoft.com/net/download/core)は `project.json` Visual Studio 2015 を使用して .net Core アプリケーションをサポートまたは開発しなくなりました。</span><span class="sxs-lookup"><span data-stu-id="37466-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="37466-135">[project.json から csproj への移行](/dotnet/articles/core/migration/)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="37466-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="37466-136">Visual Studio を使用している場合は、 [Visual studio 2017](https://www.visualstudio.com/downloads/)にアップグレードすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="37466-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="37466-137">UWP: バインドリダイレクトの追加</span><span class="sxs-lookup"><span data-stu-id="37466-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="37466-138">ユニバーサル Windows プラットフォーム (UWP) プロジェクトで EF コマンドを実行しようとすると、次のエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="37466-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="37466-139">UWP プロジェクトにバインドリダイレクトを手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37466-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="37466-140">プロジェクトルートフォルダーにという名前のファイルを作成 `App.config` し、適切なアセンブリバージョンにリダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="37466-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

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
