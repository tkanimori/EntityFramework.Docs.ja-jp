---
title: 移行 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: c87864b3430d3cd42729c13ddde33c0cd9de9308
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672989"
---
# <a name="migrations"></a><span data-ttu-id="471ad-102">移行</span><span class="sxs-lookup"><span data-stu-id="471ad-102">Migrations</span></span>

<span data-ttu-id="471ad-103">データ モデルは開発中に変更され、データベースと同期しなくなります。</span><span class="sxs-lookup"><span data-stu-id="471ad-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="471ad-104">データベースを削除して、モデルと一致する新しいものを EF に作成させることもできますが、この手順ではデータが失われてしまいます。</span><span class="sxs-lookup"><span data-stu-id="471ad-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="471ad-105">EF Core の移行機能では、データベースの既存のデータを維持しながら、アプリケーションのデータ モデルとデータベース スキーマを同期した状態で、データベース スキーマを増分的に更新することができます。</span><span class="sxs-lookup"><span data-stu-id="471ad-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="471ad-106">移行には、次のタスクの助けとなるコマンドライン ツールと API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="471ad-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="471ad-107">[移行を作成します](#create-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="471ad-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="471ad-108">データベースを更新できるモデルの変更のセットと同期できるコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="471ad-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="471ad-109">[データベースを更新します](#update-the-database)。</span><span class="sxs-lookup"><span data-stu-id="471ad-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="471ad-110">データベース スキーマを更新し、保留中となって移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="471ad-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="471ad-111">[移行コードをカスタマイズします](#customize-migration-code)。</span><span class="sxs-lookup"><span data-stu-id="471ad-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="471ad-112">生成されたコードをときどき変更したり補完したりする必要があります。</span><span class="sxs-lookup"><span data-stu-id="471ad-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="471ad-113">[移行を削除します](#remove-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="471ad-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="471ad-114">生成されたコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="471ad-114">Delete the generated code.</span></span>
* <span data-ttu-id="471ad-115">[移行を元に戻します](#revert-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="471ad-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="471ad-116">データベースの変更をやり直します。</span><span class="sxs-lookup"><span data-stu-id="471ad-116">Undo the database changes.</span></span>
* <span data-ttu-id="471ad-117">[SQL スクリプトを生成します](#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="471ad-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="471ad-118">実稼働データベースを更新したり、移行コードをトラブルシューティングしたりするためにスクリプトが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="471ad-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="471ad-119">[実行時に移行を適用します](#apply-migrations-at-runtime)。</span><span class="sxs-lookup"><span data-stu-id="471ad-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="471ad-120">デザイン時の更新やスクリプトの実行が最適なオプションでない場合、`Migrate()` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="471ad-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

> [!TIP]
> <span data-ttu-id="471ad-121">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-121">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="install-the-tools"></a><span data-ttu-id="471ad-122">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="471ad-122">Install the tools</span></span>

<span data-ttu-id="471ad-123">[コマンドライン ツール](xref:core/miscellaneous/cli/index)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="471ad-123">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="471ad-124">Visual Studio では、[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="471ad-124">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="471ad-125">その他の開発環境では、[.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet)を選択します。</span><span class="sxs-lookup"><span data-stu-id="471ad-125">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

## <a name="create-a-migration"></a><span data-ttu-id="471ad-126">移行を作成する</span><span class="sxs-lookup"><span data-stu-id="471ad-126">Create a migration</span></span>

<span data-ttu-id="471ad-127">[最初のモデルを定義](xref:core/modeling/index)したら、次にデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="471ad-127">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="471ad-128">最初の移行を追加するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="471ad-128">To add an initial migration, run the following command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-129">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-129">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-130">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="471ad-131">**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-131">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="471ad-132">**XXXXXXXXXXXXXX_InitialCreate.cs**--メインの移行ファイル。</span><span class="sxs-lookup"><span data-stu-id="471ad-132">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="471ad-133">(`Up()` で) 移行を適用し、(`Down()` で) それを元に戻すために必要な操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="471ad-133">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="471ad-134">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--移行メタデータ ファイル。</span><span class="sxs-lookup"><span data-stu-id="471ad-134">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="471ad-135">EF によって使用される情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="471ad-135">Contains information used by EF.</span></span>
* <span data-ttu-id="471ad-136">**MyContextModelSnapshot.cs**--現在のモデルのスナップショット。</span><span class="sxs-lookup"><span data-stu-id="471ad-136">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="471ad-137">次の移行を追加するときの変更内容の決定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-137">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="471ad-138">変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-138">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="471ad-139">名前空間</span><span class="sxs-lookup"><span data-stu-id="471ad-139">Namespaces</span></span>

<span data-ttu-id="471ad-140">移行ファイルは自由に移動し、手動で名前空間を変更できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-140">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="471ad-141">新しい移行は前回の移行の兄弟として作成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-141">New migrations are created as siblings of the last migration.</span></span>

<span data-ttu-id="471ad-142">また、`-Namespace` (パッケージ マネージャー コンソール) または `--namespace` (.NET Core CLI) を使用して、生成時に名前空間を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="471ad-142">Alternatively you can use `-Namespace` (Package Manager Console) or `--namespace` (.NET Core CLI) to specify the namespace at generation time.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="update-the-database"></a><span data-ttu-id="471ad-145">データベースを更新する</span><span class="sxs-lookup"><span data-stu-id="471ad-145">Update the database</span></span>

<span data-ttu-id="471ad-146">次に、移行をデータベースに適用し、スキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="471ad-146">Next, apply the migration to the database to create the schema.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-147">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-147">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-148">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="471ad-149">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="471ad-149">Customize migration code</span></span>

<span data-ttu-id="471ad-150">EF Core モデルの変更後、データベース スキーマは同期していない状態になります。それを最新の状態にするには、別の移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="471ad-150">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="471ad-151">移行名は、バージョン管理システムのコミット メッセージのように使用できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-151">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="471ad-152">たとえば、変更するのがレビュー用の新しいエンティティ クラスである場合、*AddProductReviews* などの名前を選択します。</span><span class="sxs-lookup"><span data-stu-id="471ad-152">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-153">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-154">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

<span data-ttu-id="471ad-155">移行が (それのためにコードが生成され) スキャフォールディングされたら、コードが正しいか確認し、それを正しく適用するために必要な任意の操作を追加、削除または変更します。</span><span class="sxs-lookup"><span data-stu-id="471ad-155">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="471ad-156">たとえば、移行には次の操作が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="471ad-156">For example, a migration might contain the following operations:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="471ad-157">これらの操作はデータベース スキーマに互換性を与えますが、既存の顧客名を保持しません。</span><span class="sxs-lookup"><span data-stu-id="471ad-157">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="471ad-158">改善するには、次のように書き直します。</span><span class="sxs-lookup"><span data-stu-id="471ad-158">To make it better, rewrite it as follows.</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> <span data-ttu-id="471ad-159">移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。</span><span class="sxs-lookup"><span data-stu-id="471ad-159">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="471ad-160">その警告が表示されたら、移行コードが正しいことを特に確認してください。</span><span class="sxs-lookup"><span data-stu-id="471ad-160">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="471ad-161">適切なコマンドを利用し、データベースに移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="471ad-161">Apply the migration to the database using the appropriate command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-162">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-163">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a><span data-ttu-id="471ad-164">空の移行</span><span class="sxs-lookup"><span data-stu-id="471ad-164">Empty migrations</span></span>

<span data-ttu-id="471ad-165">モデル変更を行わずに移行を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="471ad-165">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="471ad-166">この場合、新しい移行を追加すると、空のクラスのコード ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-166">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="471ad-167">EF Core モデルに直接関連しない操作を実行するようにこの移行をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="471ad-167">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="471ad-168">この方法で管理すると便利なものは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="471ad-168">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="471ad-169">フルテキスト検索</span><span class="sxs-lookup"><span data-stu-id="471ad-169">Full-Text Search</span></span>
* <span data-ttu-id="471ad-170">関数</span><span class="sxs-lookup"><span data-stu-id="471ad-170">Functions</span></span>
* <span data-ttu-id="471ad-171">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="471ad-171">Stored procedures</span></span>
* <span data-ttu-id="471ad-172">トリガー</span><span class="sxs-lookup"><span data-stu-id="471ad-172">Triggers</span></span>
* <span data-ttu-id="471ad-173">Views</span><span class="sxs-lookup"><span data-stu-id="471ad-173">Views</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="471ad-174">移行を削除する</span><span class="sxs-lookup"><span data-stu-id="471ad-174">Remove a migration</span></span>

<span data-ttu-id="471ad-175">移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。</span><span class="sxs-lookup"><span data-stu-id="471ad-175">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="471ad-176">最後の移行を削除するには、このコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="471ad-176">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-177">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-178">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="471ad-179">移行の削除後、追加のモデル変更を行い、もう一度追加できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-179">After removing the migration, you can make the additional model changes and add it again.</span></span>

## <a name="revert-a-migration"></a><span data-ttu-id="471ad-180">移行を元に戻す</span><span class="sxs-lookup"><span data-stu-id="471ad-180">Revert a migration</span></span>

<span data-ttu-id="471ad-181">移行をデータベースに既に適用しているが、元に戻す必要がある場合、同じコマンドを使用して移行を適用できますが、ロールバックする移行の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="471ad-181">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-182">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-182">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-183">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a><span data-ttu-id="471ad-184">SQL スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="471ad-184">Generate SQL scripts</span></span>

<span data-ttu-id="471ad-185">移行をデバッグするか、それを実稼働データベースに展開するとき、SQL スクリプトを生成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="471ad-185">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="471ad-186">このスクリプトはさらに見直して正しいかどうかを確認し、実稼働データベースのニーズに合わせて調整できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-186">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="471ad-187">このスクリプトは、展開テクノロジとの連動でも利用できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-187">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="471ad-188">基本コマンドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="471ad-188">The basic command is as follows.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="471ad-189">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="471ad-189">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="471ad-190">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="471ad-190">Basic Usage</span></span>
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="471ad-191">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="471ad-191">With From (to implied)</span></span>
<span data-ttu-id="471ad-192">これにより、この移行から最新の移行までの SQL スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-192">This will generate a SQL script from this migration to the latest migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="471ad-193">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="471ad-193">With From and To</span></span>
<span data-ttu-id="471ad-194">これにより、`from` 移行から、指定された `to` までの SQL スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-194">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="471ad-195">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="471ad-195">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="471ad-196">*データ損失の可能性のあるシナリオには注意してください。*</span><span class="sxs-lookup"><span data-stu-id="471ad-196">*Please take note of potential data loss scenarios.*</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="471ad-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="471ad-197">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="471ad-198">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="471ad-198">Basic Usage</span></span>
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="471ad-199">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="471ad-199">With From (to implied)</span></span>
<span data-ttu-id="471ad-200">これにより、この移行から最新の移行までの SQL スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-200">This will generate a SQL script from this migration to the latest migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="471ad-201">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="471ad-201">With From and To</span></span>
<span data-ttu-id="471ad-202">これにより、`from` 移行から、指定された `to` までの SQL スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="471ad-202">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="471ad-203">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="471ad-203">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="471ad-204">*データ損失の可能性のあるシナリオには注意してください。*</span><span class="sxs-lookup"><span data-stu-id="471ad-204">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="471ad-205">このコマンドにはいくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="471ad-205">There are several options to this command.</span></span>

<span data-ttu-id="471ad-206">**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="471ad-206">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="471ad-207">移行が適用されていない場合、`0` を指定します (これは既定です)。</span><span class="sxs-lookup"><span data-stu-id="471ad-207">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="471ad-208">**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。</span><span class="sxs-lookup"><span data-stu-id="471ad-208">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="471ad-209">これは既定でプロジェクトの最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="471ad-209">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="471ad-210">**idempotent** スクリプトをオプションで生成できます。</span><span class="sxs-lookup"><span data-stu-id="471ad-210">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="471ad-211">このスクリプトは、データベースにまだ適用されていない移行のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="471ad-211">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="471ad-212">これは、データベースに適用された最後の移行が正確にわからない場合、あるいは複数のデータベースに展開するとき、いずれも移行が異なる可能性がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="471ad-212">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="471ad-213">実行時に移行を適用する</span><span class="sxs-lookup"><span data-stu-id="471ad-213">Apply migrations at runtime</span></span>

<span data-ttu-id="471ad-214">起動中または最初の実行中、実行時に移行を適用するアプリがあります。</span><span class="sxs-lookup"><span data-stu-id="471ad-214">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="471ad-215">`Migrate()` メソッドを使用してこれを行います。</span><span class="sxs-lookup"><span data-stu-id="471ad-215">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="471ad-216">このメソッドは、より高度なシナリオで利用される `IMigrator` サービスの上でビルドされます。</span><span class="sxs-lookup"><span data-stu-id="471ad-216">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="471ad-217">アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。</span><span class="sxs-lookup"><span data-stu-id="471ad-217">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * <span data-ttu-id="471ad-218">この方法は万人向けではありません。</span><span class="sxs-lookup"><span data-stu-id="471ad-218">This approach isn't for everyone.</span></span> <span data-ttu-id="471ad-219">ローカル データベースを利用するアプリには最適ですが、ほとんどのアプリケーションでは、SQL スクリプトの生成など、より堅牢な展開戦略が必要になります。</span><span class="sxs-lookup"><span data-stu-id="471ad-219">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="471ad-220">`Migrate()` の前に `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="471ad-220">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="471ad-221">`EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。</span><span class="sxs-lookup"><span data-stu-id="471ad-221">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

## <a name="next-steps"></a><span data-ttu-id="471ad-222">次の手順</span><span class="sxs-lookup"><span data-stu-id="471ad-222">Next steps</span></span>

<span data-ttu-id="471ad-223">詳細については、「<xref:core/miscellaneous/cli/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="471ad-223">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
