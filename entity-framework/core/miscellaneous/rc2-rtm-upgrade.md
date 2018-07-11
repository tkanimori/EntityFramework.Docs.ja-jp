---
title: RTM - に 1.0 RC2 をコア EF からアップグレードする EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 9561eac253517188251fece9a03f434482246051
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949058"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="33b8a-102">EF Core 1.0 RC2 から RTM にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="33b8a-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="33b8a-103">この記事では、1.0.0 に RC2 パッケージで構築されたアプリケーションの移動のガイダンスを提供します。 RTM。</span><span class="sxs-lookup"><span data-stu-id="33b8a-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="33b8a-104">パッケージのバージョン</span><span class="sxs-lookup"><span data-stu-id="33b8a-104">Package Versions</span></span>

<span data-ttu-id="33b8a-105">アプリケーションに通常インストールされる最上位レベルのパッケージの名前 RC2 から RTM の間で変更していません。</span><span class="sxs-lookup"><span data-stu-id="33b8a-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="33b8a-106">**RTM バージョンにインストールされているパッケージをアップグレードする必要があります。**</span><span class="sxs-lookup"><span data-stu-id="33b8a-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="33b8a-107">ランタイム パッケージ (たとえば、 `Microsoft.EntityFrameworkCore.SqlServer`) から変更`1.0.0-rc2-final`に`1.0.0`します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="33b8a-108">`Microsoft.EntityFrameworkCore.Tools`からパッケージが変更された`1.0.0-preview1-final`に`1.0.0-preview2-final`します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="33b8a-109">ツールはまだプレリリース版であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="33b8a-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="33b8a-110">既存の移行が maxLength の追加を必要があります。</span><span class="sxs-lookup"><span data-stu-id="33b8a-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="33b8a-111">移行では、列定義のように RC2 では、`table.Column<string>(nullable: true)`され、列の長さが、移行の分離コードで格納するいくつかのメタデータで検索します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="33b8a-112">長さに今すぐスキャフォールディングされたコードに含まれている RTM では、`table.Column<string>(maxLength: 450, nullable: true)`します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="33b8a-113">RTM を使用する前にスキャフォールディングされた、既存の移行はありません、`maxLength`引数が指定されました。</span><span class="sxs-lookup"><span data-stu-id="33b8a-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="33b8a-114">つまり、データベースでサポートされている最大の長さが使用されます (`nvarchar(max)` SQL サーバー上)。</span><span class="sxs-lookup"><span data-stu-id="33b8a-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="33b8a-115">これは問題ありませんが、一部の列、列、キー、外部のキーの一部である可能性があります。 またはインデックスは、最大長を含むように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33b8a-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="33b8a-116">通常、450 は最大長は、キー、外部キーは、使用され、インデックス列を作成します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="33b8a-117">モデルで明示的に長さを構成した場合がその長さ代わりに使用します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="33b8a-118">**ASP.NET Identity**</span><span class="sxs-lookup"><span data-stu-id="33b8a-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="33b8a-119">この変更の影響を ASP.NET Identity を使用し、前から作成されたプロジェクトのプロジェクト テンプレートを RTM。</span><span class="sxs-lookup"><span data-stu-id="33b8a-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="33b8a-120">プロジェクト テンプレートには、データベースの作成に使用される移行が含まれています。</span><span class="sxs-lookup"><span data-stu-id="33b8a-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="33b8a-121">この移行を編集の最大長を指定する必要があります`256`次の列にします。</span><span class="sxs-lookup"><span data-stu-id="33b8a-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="33b8a-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="33b8a-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="33b8a-123">name</span><span class="sxs-lookup"><span data-stu-id="33b8a-123">Name</span></span>

    * <span data-ttu-id="33b8a-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="33b8a-124">NormalizedName</span></span>

*  <span data-ttu-id="33b8a-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="33b8a-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="33b8a-126">電子メール</span><span class="sxs-lookup"><span data-stu-id="33b8a-126">Email</span></span>

   * <span data-ttu-id="33b8a-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="33b8a-127">NormalizedEmail</span></span>

   * <span data-ttu-id="33b8a-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="33b8a-128">NormalizedUserName</span></span>

   * <span data-ttu-id="33b8a-129">UserName</span><span class="sxs-lookup"><span data-stu-id="33b8a-129">UserName</span></span>

<span data-ttu-id="33b8a-130">最初の移行がデータベースに適用されるときに、この変更を行う失敗は次の例外が発生されます。</span><span class="sxs-lookup"><span data-stu-id="33b8a-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="33b8a-131">.NET core: project.json で"imports"を削除します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="33b8a-132">追加する必要 RC2 を使用して .NET Core を対象とした場合`imports`を一部の .NET Standard をサポートしない EF Core の依存関係の一時的な回避策として、project.json にします。</span><span class="sxs-lookup"><span data-stu-id="33b8a-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="33b8a-133">これらは削除ようになりましたことができます。</span><span class="sxs-lookup"><span data-stu-id="33b8a-133">These can now be removed.</span></span>

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
> <span data-ttu-id="33b8a-134">バージョン 1.0 の時点で RTM、 [.NET Core SDK](https://www.microsoft.com/net/download/core)はサポートしなくなりました`project.json`または Visual Studio 2015 を使用して .NET Core アプリケーションを開発します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="33b8a-135">[project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="33b8a-135">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="33b8a-136">Visual Studio を使用している場合にアップグレードする勧め[Visual Studio 2017](https://www.visualstudio.com/downloads/)します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="33b8a-137">UWP: バインド リダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="33b8a-138">ユニバーサル Windows プラットフォーム (UWP) プロジェクトの結果、次のエラーで EF コマンドを実行しようとしています。</span><span class="sxs-lookup"><span data-stu-id="33b8a-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

<span data-ttu-id="33b8a-139">UWP プロジェクトにバインド リダイレクトを手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33b8a-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="33b8a-140">という名前のファイルを作成する`App.config`プロジェクトのルート フォルダーと、適切なアセンブリのバージョンにリダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="33b8a-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

``` xml
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
