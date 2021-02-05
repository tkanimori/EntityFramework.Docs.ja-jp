---
title: 移行の管理-EF Core
description: Entity Framework Core を使用したデータベーススキーマの移行の追加、削除、および管理
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: ef5e1b9bb10d6f1cd428db2fee327ec513c3f528
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "95003277"
---
# <a name="managing-migrations"></a><span data-ttu-id="6f06f-103">移行の管理</span><span class="sxs-lookup"><span data-stu-id="6f06f-103">Managing Migrations</span></span>

<span data-ttu-id="6f06f-104">モデルが変更されると、通常の開発の一環として移行が追加および削除され、移行ファイルがプロジェクトのソース管理にチェックインされます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="6f06f-105">移行を管理するには、まず [EF Core コマンドラインツール](xref:core/cli/index)をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="6f06f-106">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="6f06f-107">移行を追加する</span><span class="sxs-lookup"><span data-stu-id="6f06f-107">Add a migration</span></span>

<span data-ttu-id="6f06f-108">モデルが変更されたら、その変更の移行を追加できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6f06f-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6f06f-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="6f06f-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f06f-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="6f06f-111">移行名は、バージョン管理システムのコミット メッセージのように使用できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="6f06f-112">たとえば、変更がエンティティの新しいプロパティである場合、 *Addブログ* のような名前を選択でき `CreatedTimestamp` `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="6f06f-113">**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="6f06f-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp**、メインの移行ファイルです。</span><span class="sxs-lookup"><span data-stu-id="6f06f-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="6f06f-115">(`Up` で) 移行を適用し、(`Down` で) それを元に戻すために必要な操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="6f06f-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp**、移行メタデータファイルです。</span><span class="sxs-lookup"><span data-stu-id="6f06f-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="6f06f-117">EF によって使用される情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-117">Contains information used by EF.</span></span>
* <span data-ttu-id="6f06f-118">**MyContextModelSnapshot.cs**--現在のモデルのスナップショット。</span><span class="sxs-lookup"><span data-stu-id="6f06f-118">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="6f06f-119">次の移行を追加するときの変更内容の決定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="6f06f-120">変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="6f06f-121">名前空間</span><span class="sxs-lookup"><span data-stu-id="6f06f-121">Namespaces</span></span>

<span data-ttu-id="6f06f-122">移行ファイルは自由に移動し、手動で名前空間を変更できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="6f06f-123">新しい移行は前回の移行の兄弟として作成されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="6f06f-124">または、次のように生成時にディレクトリを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-124">Alternatively, you can specify the directory at generation time as follows:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="6f06f-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6f06f-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> <span data-ttu-id="6f06f-126">EF Core 5.0 では、を使用して、ディレクトリとは無関係に名前空間を変更することもでき `--namespace` ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-126">In EF Core 5.0, you can also change the namespace independently of the directory using `--namespace`.</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="6f06f-127">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f06f-127">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> <span data-ttu-id="6f06f-128">EF Core 5.0 では、を使用して、ディレクトリとは無関係に名前空間を変更することもでき `-Namespace` ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-128">In EF Core 5.0, you can also change the namespace independently of the directory using `-Namespace`.</span></span>

***

## <a name="customize-migration-code"></a><span data-ttu-id="6f06f-129">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="6f06f-129">Customize migration code</span></span>

<span data-ttu-id="6f06f-130">EF Core 通常は正確な移行を作成しますが、コードを常に確認し、必要な変更に対応していることを確認してください。場合によっては、これを行う必要もあります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-130">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="6f06f-131">列名の変更</span><span class="sxs-lookup"><span data-stu-id="6f06f-131">Column renames</span></span>

<span data-ttu-id="6f06f-132">移行のカスタマイズが必要な例の1つは、プロパティの名前を変更する場合です。</span><span class="sxs-lookup"><span data-stu-id="6f06f-132">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="6f06f-133">たとえば、プロパティの名前をからに変更すると `Name` `FullName` 、EF Core によって次の移行が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-133">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="6f06f-134">EF Core は、通常、列を削除し、新しい列 (2 つの異なる変更) を作成し、列の名前を変更する必要があるかどうかを知ることができません。</span><span class="sxs-lookup"><span data-stu-id="6f06f-134">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="6f06f-135">上記の移行がそのように適用されている場合、顧客名はすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-135">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="6f06f-136">列の名前を変更するには、上記の生成された移行を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-136">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="6f06f-137">移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-137">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="6f06f-138">その警告が表示されたら、移行コードが正しいことを特に確認してください。</span><span class="sxs-lookup"><span data-stu-id="6f06f-138">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="6f06f-139">生の SQL の追加</span><span class="sxs-lookup"><span data-stu-id="6f06f-139">Adding raw SQL</span></span>

<span data-ttu-id="6f06f-140">列の名前の変更は、組み込みの API を使用して行うことができますが、多くの場合、可能ではありません。</span><span class="sxs-lookup"><span data-stu-id="6f06f-140">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="6f06f-141">たとえば、既存の `FirstName` `LastName` プロパティとプロパティを1つの新しいプロパティに置き換えることができ `FullName` ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-141">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="6f06f-142">EF Core によって生成される移行は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-142">The migration generated by EF Core will be the following:</span></span>

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

<span data-ttu-id="6f06f-143">以前と同様に、望ましくないデータ損失が発生します。</span><span class="sxs-lookup"><span data-stu-id="6f06f-143">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="6f06f-144">古い列からデータを転送するには、次のように移行を再配置し、生の SQL 操作を導入します。</span><span class="sxs-lookup"><span data-stu-id="6f06f-144">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

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

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="6f06f-145">未加工の SQL を使用した任意の変更</span><span class="sxs-lookup"><span data-stu-id="6f06f-145">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="6f06f-146">生の SQL を使用すると、EF Core 認識されないデータベースオブジェクトを管理することもできます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-146">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="6f06f-147">これを行うには、モデルを変更せずに移行を追加します。空の移行が生成されます。その後、生の SQL 操作を設定できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-147">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="6f06f-148">たとえば、次の移行では、SQL Server のストアドプロシージャが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-148">For example, the following migration creates a SQL Server stored procedure:</span></span>

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> <span data-ttu-id="6f06f-149">`EXEC` ステートメントが SQL バッチ内の最初のステートメントまたは1つのステートメントである必要がある場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6f06f-149">`EXEC` is used when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="6f06f-150">また、参照されている列がテーブルに存在しない場合に発生する可能性があるべき等移行スクリプトで、パーサーエラーを回避するためにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-150">It can also be used to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="6f06f-151">これを使用すると、次のようなデータベースのあらゆる側面を管理できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-151">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="6f06f-152">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="6f06f-152">Stored procedures</span></span>
* <span data-ttu-id="6f06f-153">フルテキスト検索</span><span class="sxs-lookup"><span data-stu-id="6f06f-153">Full-Text Search</span></span>
* <span data-ttu-id="6f06f-154">関数</span><span class="sxs-lookup"><span data-stu-id="6f06f-154">Functions</span></span>
* <span data-ttu-id="6f06f-155">トリガー</span><span class="sxs-lookup"><span data-stu-id="6f06f-155">Triggers</span></span>
* <span data-ttu-id="6f06f-156">ビュー</span><span class="sxs-lookup"><span data-stu-id="6f06f-156">Views</span></span>

<span data-ttu-id="6f06f-157">ほとんどの場合、移行を適用すると、EF Core によって各移行が独自のトランザクションで自動的にラップされます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-157">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="6f06f-158">残念ながら、一部のデータベースでは、一部の移行操作をトランザクション内で実行することはできません。このような場合は、に渡すことによってトランザクションをオプトアウトすることができ `suppressTransaction: true` `migrationBuilder.Sql` ます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-158">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="6f06f-159">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-159">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="6f06f-160">移行を削除する</span><span class="sxs-lookup"><span data-stu-id="6f06f-160">Remove a migration</span></span>

<span data-ttu-id="6f06f-161">移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-161">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="6f06f-162">最後の移行を削除するには、このコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6f06f-162">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6f06f-163">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6f06f-163">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="6f06f-164">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f06f-164">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="6f06f-165">移行の削除後、追加のモデル変更を行い、もう一度追加できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-165">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="6f06f-166">実稼働データベースに既に適用されている移行は削除しないように注意してください。</span><span class="sxs-lookup"><span data-stu-id="6f06f-166">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="6f06f-167">そうしないと、それを元に戻すことができなくなり、その後の移行によって想定される内容が壊れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-167">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="6f06f-168">移行の一覧表示</span><span class="sxs-lookup"><span data-stu-id="6f06f-168">Listing migrations</span></span>

<span data-ttu-id="6f06f-169">次のように、すべての既存の移行を一覧表示できます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-169">You can list all existing migrations as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6f06f-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6f06f-170">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[<span data-ttu-id="6f06f-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f06f-171">Visual Studio</span></span>](#tab/vs)

> [!NOTE]
> <span data-ttu-id="6f06f-172">このコマンドは EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="6f06f-172">This command was introduced in EF Core 5.0.</span></span>

```powershell
Get-Migration
```

***

## <a name="resetting-all-migrations"></a><span data-ttu-id="6f06f-173">すべての移行をリセットしています</span><span class="sxs-lookup"><span data-stu-id="6f06f-173">Resetting all migrations</span></span>

<span data-ttu-id="6f06f-174">極端なケースでは、すべての移行を削除してからやり直すことが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-174">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="6f06f-175">これは、 **移行** フォルダーを削除して、データベースを削除することで簡単に行うことができます。この時点で、新しい初期移行を作成できます。これには、現在のスキーマ全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-175">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="6f06f-176">すべての移行をリセットし、データを失うことなく1つの移行を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-176">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="6f06f-177">これは "スカッシュ" と呼ばれることもあり、手動での作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="6f06f-177">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="6f06f-178">**マイグレーション** フォルダーの削除</span><span class="sxs-lookup"><span data-stu-id="6f06f-178">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="6f06f-179">新しい移行を作成し、その移行用の SQL スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="6f06f-179">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="6f06f-180">データベースで、移行履歴テーブルからすべての行を削除します。</span><span class="sxs-lookup"><span data-stu-id="6f06f-180">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="6f06f-181">1つの行を移行履歴に挿入して、最初の移行が既に適用されていることを記録します。これは、テーブルが既に存在しているためです。</span><span class="sxs-lookup"><span data-stu-id="6f06f-181">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="6f06f-182">Insert SQL は、上記で生成された SQL スクリプトの最後の操作です。</span><span class="sxs-lookup"><span data-stu-id="6f06f-182">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="6f06f-183">**移行フォルダーが** 削除されると、[カスタム移行コード](#customize-migration-code)は失われます。</span><span class="sxs-lookup"><span data-stu-id="6f06f-183">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="6f06f-184">すべてのカスタマイズを保存するには、新しい初期移行に手動で適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6f06f-184">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
