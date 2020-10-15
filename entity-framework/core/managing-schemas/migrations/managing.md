---
title: 移行の管理-EF Core
description: Entity Framework Core を使用したデータベーススキーマの移行の追加、削除、および管理
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: fdfda6f3dea306fbbc343c1be3f4d5754d1f65c4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062062"
---
# <a name="managing-migrations"></a><span data-ttu-id="b472b-103">移行の管理</span><span class="sxs-lookup"><span data-stu-id="b472b-103">Managing Migrations</span></span>

<span data-ttu-id="b472b-104">モデルが変更されると、通常の開発の一環として移行が追加および削除され、移行ファイルがプロジェクトのソース管理にチェックインされます。</span><span class="sxs-lookup"><span data-stu-id="b472b-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="b472b-105">移行を管理するには、まず [EF Core コマンドラインツール](xref:core/miscellaneous/cli/index)をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b472b-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="b472b-106">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="b472b-107">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="b472b-107">Add a migration</span></span>

<span data-ttu-id="b472b-108">モデルが変更されたら、その変更の移行を追加できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b472b-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b472b-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="b472b-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b472b-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="b472b-111">移行名は、バージョン管理システムのコミット メッセージのように使用できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="b472b-112">たとえば、変更がエンティティの新しいプロパティである場合、 *Addブログ* のような名前を選択でき `CreatedTimestamp` `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="b472b-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="b472b-113">**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="b472b-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp**、メインの移行ファイルです。</span><span class="sxs-lookup"><span data-stu-id="b472b-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="b472b-115">(`Up` で) 移行を適用し、(`Down` で) それを元に戻すために必要な操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b472b-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="b472b-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp**、移行メタデータファイルです。</span><span class="sxs-lookup"><span data-stu-id="b472b-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="b472b-117">EF によって使用される情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b472b-117">Contains information used by EF.</span></span>
* <span data-ttu-id="b472b-118">**MyContextModelSnapshot.cs**--現在のモデルのスナップショット。</span><span class="sxs-lookup"><span data-stu-id="b472b-118">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="b472b-119">次の移行を追加するときの変更内容の決定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="b472b-120">変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="b472b-121">名前空間</span><span class="sxs-lookup"><span data-stu-id="b472b-121">Namespaces</span></span>

<span data-ttu-id="b472b-122">移行ファイルは自由に移動し、手動で名前空間を変更できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="b472b-123">新しい移行は前回の移行の兄弟として作成されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="b472b-124">または、次のように、生成時に名前空間を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="b472b-124">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b472b-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b472b-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="b472b-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b472b-126">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="b472b-127">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="b472b-127">Customize migration code</span></span>

<span data-ttu-id="b472b-128">EF Core 通常は正確な移行を作成しますが、コードを常に確認し、必要な変更に対応していることを確認してください。場合によっては、これを行う必要もあります。</span><span class="sxs-lookup"><span data-stu-id="b472b-128">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="b472b-129">列名の変更</span><span class="sxs-lookup"><span data-stu-id="b472b-129">Column renames</span></span>

<span data-ttu-id="b472b-130">移行のカスタマイズが必要な例の1つは、プロパティの名前を変更する場合です。</span><span class="sxs-lookup"><span data-stu-id="b472b-130">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="b472b-131">たとえば、プロパティの名前をからに変更すると `Name` `FullName` 、EF Core によって次の移行が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-131">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="b472b-132">EF Core は、通常、列を削除し、新しい列 (2 つの異なる変更) を作成し、列の名前を変更する必要があるかどうかを知ることができません。</span><span class="sxs-lookup"><span data-stu-id="b472b-132">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="b472b-133">上記の移行がそのように適用されている場合、顧客名はすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="b472b-133">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="b472b-134">列の名前を変更するには、上記の生成された移行を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b472b-134">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="b472b-135">移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。</span><span class="sxs-lookup"><span data-stu-id="b472b-135">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="b472b-136">その警告が表示されたら、移行コードが正しいことを特に確認してください。</span><span class="sxs-lookup"><span data-stu-id="b472b-136">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="b472b-137">生の SQL の追加</span><span class="sxs-lookup"><span data-stu-id="b472b-137">Adding raw SQL</span></span>

<span data-ttu-id="b472b-138">列の名前の変更は、組み込みの API を使用して行うことができますが、多くの場合、可能ではありません。</span><span class="sxs-lookup"><span data-stu-id="b472b-138">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="b472b-139">たとえば、既存の `FirstName` `LastName` プロパティとプロパティを1つの新しいプロパティに置き換えることができ `FullName` ます。</span><span class="sxs-lookup"><span data-stu-id="b472b-139">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="b472b-140">EF Core によって生成される移行は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b472b-140">The migration generated by EF Core will be the following:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="b472b-141">以前と同様に、望ましくないデータ損失が発生します。</span><span class="sxs-lookup"><span data-stu-id="b472b-141">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="b472b-142">古い列からデータを転送するには、次のように移行を再配置し、生の SQL 操作を導入します。</span><span class="sxs-lookup"><span data-stu-id="b472b-142">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="b472b-143">未加工の SQL を使用した任意の変更</span><span class="sxs-lookup"><span data-stu-id="b472b-143">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="b472b-144">生の SQL を使用すると、EF Core 認識されないデータベースオブジェクトを管理することもできます。</span><span class="sxs-lookup"><span data-stu-id="b472b-144">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="b472b-145">これを行うには、モデルを変更せずに移行を追加します。空の移行が生成されます。その後、生の SQL 操作を設定できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-145">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="b472b-146">たとえば、次の移行では、SQL Server のストアドプロシージャが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b472b-146">For example, the following migration creates a SQL Server stored procedure:</span></span>

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="b472b-147">これを使用すると、次のようなデータベースのあらゆる側面を管理できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-147">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="b472b-148">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="b472b-148">Stored procedures</span></span>
* <span data-ttu-id="b472b-149">フルテキスト検索</span><span class="sxs-lookup"><span data-stu-id="b472b-149">Full-Text Search</span></span>
* <span data-ttu-id="b472b-150">関数</span><span class="sxs-lookup"><span data-stu-id="b472b-150">Functions</span></span>
* <span data-ttu-id="b472b-151">トリガー</span><span class="sxs-lookup"><span data-stu-id="b472b-151">Triggers</span></span>
* <span data-ttu-id="b472b-152">ビュー</span><span class="sxs-lookup"><span data-stu-id="b472b-152">Views</span></span>

<span data-ttu-id="b472b-153">ほとんどの場合、移行を適用すると、EF Core によって各移行が独自のトランザクションで自動的にラップされます。</span><span class="sxs-lookup"><span data-stu-id="b472b-153">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="b472b-154">残念ながら、一部のデータベースでは、一部の移行操作をトランザクション内で実行することはできません。このような場合は、に渡すことによってトランザクションをオプトアウトすることができ `suppressTransaction: true` `migrationBuilder.Sql` ます。</span><span class="sxs-lookup"><span data-stu-id="b472b-154">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="b472b-155">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-155">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="b472b-156">移行を削除する</span><span class="sxs-lookup"><span data-stu-id="b472b-156">Remove a migration</span></span>

<span data-ttu-id="b472b-157">移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。</span><span class="sxs-lookup"><span data-stu-id="b472b-157">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="b472b-158">最後の移行を削除するには、このコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b472b-158">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b472b-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b472b-159">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="b472b-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b472b-160">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="b472b-161">移行の削除後、追加のモデル変更を行い、もう一度追加できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-161">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="b472b-162">実稼働データベースに既に適用されている移行は削除しないように注意してください。</span><span class="sxs-lookup"><span data-stu-id="b472b-162">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="b472b-163">そうしないと、それを元に戻すことができなくなり、その後の移行によって想定される内容が壊れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b472b-163">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="b472b-164">移行の一覧表示</span><span class="sxs-lookup"><span data-stu-id="b472b-164">Listing migrations</span></span>

<span data-ttu-id="b472b-165">次のように、すべての既存の移行を一覧表示できます。</span><span class="sxs-lookup"><span data-stu-id="b472b-165">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="b472b-166">すべての移行をリセットしています</span><span class="sxs-lookup"><span data-stu-id="b472b-166">Resetting all migrations</span></span>

<span data-ttu-id="b472b-167">極端なケースでは、すべての移行を削除してからやり直すことが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b472b-167">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="b472b-168">これは、 **移行** フォルダーを削除して、データベースを削除することで簡単に行うことができます。この時点で、新しい初期移行を作成できます。これには、現在のスキーマ全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b472b-168">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="b472b-169">すべての移行をリセットし、データを失うことなく1つの移行を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="b472b-169">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="b472b-170">これは "スカッシュ" と呼ばれることもあり、手動での作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="b472b-170">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="b472b-171">**マイグレーション**フォルダーの削除</span><span class="sxs-lookup"><span data-stu-id="b472b-171">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="b472b-172">新しい移行を作成し、その移行用の SQL スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="b472b-172">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="b472b-173">データベースで、移行履歴テーブルからすべての行を削除します。</span><span class="sxs-lookup"><span data-stu-id="b472b-173">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="b472b-174">1つの行を移行履歴に挿入して、最初の移行が既に適用されていることを記録します。これは、テーブルが既に存在しているためです。</span><span class="sxs-lookup"><span data-stu-id="b472b-174">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="b472b-175">Insert SQL は、上記で生成された SQL スクリプトの最後の操作です。</span><span class="sxs-lookup"><span data-stu-id="b472b-175">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="b472b-176">**移行フォルダーが**削除されると、[カスタム移行コード](#customize-migration-code)は失われます。</span><span class="sxs-lookup"><span data-stu-id="b472b-176">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="b472b-177">すべてのカスタマイズを保存するには、新しい初期移行に手動で適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b472b-177">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
