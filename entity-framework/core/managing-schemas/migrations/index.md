---
title: 移行 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: e9c4013d17a2d41772822f77b3ceba15702ffc48
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812057"
---
# <a name="migrations"></a><span data-ttu-id="d5fc6-102">移行</span><span class="sxs-lookup"><span data-stu-id="d5fc6-102">Migrations</span></span>

<span data-ttu-id="d5fc6-103">データ モデルは開発中に変更され、データベースと同期しなくなります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="d5fc6-104">データベースを削除して、モデルと一致する新しいものを EF に作成させることもできますが、この手順ではデータが失われてしまいます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="d5fc6-105">EF Core の移行機能では、データベースの既存のデータを維持しながら、アプリケーションのデータ モデルとデータベース スキーマを同期した状態で、データベース スキーマを増分的に更新することができます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="d5fc6-106">移行には、次のタスクの助けとなるコマンドライン ツールと API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="d5fc6-107">[移行を作成します](#create-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="d5fc6-108">データベースを更新できるモデルの変更のセットと同期できるコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="d5fc6-109">[データベースを更新します](#update-the-database)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="d5fc6-110">データベース スキーマを更新し、保留中となって移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="d5fc6-111">[移行コードをカスタマイズします](#customize-migration-code)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="d5fc6-112">生成されたコードをときどき変更したり補完したりする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="d5fc6-113">[移行を削除します](#remove-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="d5fc6-114">生成されたコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-114">Delete the generated code.</span></span>
* <span data-ttu-id="d5fc6-115">[移行を元に戻します](#revert-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="d5fc6-116">データベースの変更をやり直します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-116">Undo the database changes.</span></span>
* <span data-ttu-id="d5fc6-117">[SQL スクリプトを生成します](#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="d5fc6-118">実稼働データベースを更新したり、移行コードをトラブルシューティングしたりするためにスクリプトが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="d5fc6-119">[実行時に移行を適用します](#apply-migrations-at-runtime)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="d5fc6-120">デザイン時の更新やスクリプトの実行が最適なオプションでない場合、`Migrate()` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

> [!TIP]
> <span data-ttu-id="d5fc6-121">`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-121">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="install-the-tools"></a><span data-ttu-id="d5fc6-122">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="d5fc6-122">Install the tools</span></span>

<span data-ttu-id="d5fc6-123">[コマンドライン ツール](xref:core/miscellaneous/cli/index)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-123">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="d5fc6-124">Visual Studio では、[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-124">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="d5fc6-125">その他の開発環境では、[.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet)を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-125">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

## <a name="create-a-migration"></a><span data-ttu-id="d5fc6-126">移行を作成する</span><span class="sxs-lookup"><span data-stu-id="d5fc6-126">Create a migration</span></span>

<span data-ttu-id="d5fc6-127">[最初のモデルを定義](xref:core/modeling/index)したら、次にデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-127">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="d5fc6-128">最初の移行を追加するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-128">To add an initial migration, run the following command.</span></span>

``` powershell
Add-Migration InitialCreate
```

``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="d5fc6-129">**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-129">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="d5fc6-130">**XXXXXXXXXXXXXX_InitialCreate.cs**--メインの移行ファイル。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-130">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="d5fc6-131">(`Up()` で) 移行を適用し、(`Down()` で) それを元に戻すために必要な操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-131">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="d5fc6-132">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--移行メタデータ ファイル。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-132">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="d5fc6-133">EF によって使用される情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-133">Contains information used by EF.</span></span>
* <span data-ttu-id="d5fc6-134">**MyContextModelSnapshot.cs**--現在のモデルのスナップショット。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-134">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="d5fc6-135">次の移行を追加するときの変更内容の決定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-135">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="d5fc6-136">変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-136">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="d5fc6-137">移行ファイルは自由に移動したり、その名前空間を変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-137">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="d5fc6-138">新しい移行は前回の移行の兄弟として作成されます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-138">New migrations are created as siblings of the last migration.</span></span>

## <a name="update-the-database"></a><span data-ttu-id="d5fc6-139">データベースを更新する</span><span class="sxs-lookup"><span data-stu-id="d5fc6-139">Update the database</span></span>

<span data-ttu-id="d5fc6-140">次に、移行をデータベースに適用し、スキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-140">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

## <a name="customize-migration-code"></a><span data-ttu-id="d5fc6-141">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d5fc6-141">Customize migration code</span></span>

<span data-ttu-id="d5fc6-142">EF Core モデルの変更後、データベース スキーマは同期していない状態になります。それを最新の状態にするには、別の移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-142">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="d5fc6-143">移行名は、バージョン管理システムのコミット メッセージのように使用できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-143">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="d5fc6-144">たとえば、変更するのがレビュー用の新しいエンティティ クラスである場合、*AddProductReviews* などの名前を選択します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-144">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

``` powershell
Add-Migration AddProductReviews
```

``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="d5fc6-145">移行が (それのためにコードが生成され) スキャフォールディングされたら、コードが正しいか確認し、それを正しく適用するために必要な任意の操作を追加、削除または変更します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-145">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="d5fc6-146">たとえば、移行には次の操作が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-146">For example, a migration might contain the following operations:</span></span>

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

<span data-ttu-id="d5fc6-147">これらの操作はデータベース スキーマに互換性を与えますが、既存の顧客名を保持しません。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-147">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="d5fc6-148">改善するには、次のように書き直します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-148">To make it better, rewrite it as follows.</span></span>

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
> <span data-ttu-id="d5fc6-149">移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-149">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="d5fc6-150">その警告が表示されたら、移行コードが正しいことを特に確認してください。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-150">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="d5fc6-151">適切なコマンドを利用し、データベースに移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-151">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a><span data-ttu-id="d5fc6-152">空の移行</span><span class="sxs-lookup"><span data-stu-id="d5fc6-152">Empty migrations</span></span>

<span data-ttu-id="d5fc6-153">モデル変更を行わずに移行を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-153">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="d5fc6-154">この場合、新しい移行を追加すると、空のクラスのコード ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-154">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="d5fc6-155">EF Core モデルに直接関連しない操作を実行するようにこの移行をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-155">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="d5fc6-156">この方法で管理すると便利なものは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-156">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="d5fc6-157">フルテキスト検索</span><span class="sxs-lookup"><span data-stu-id="d5fc6-157">Full-Text Search</span></span>
* <span data-ttu-id="d5fc6-158">関数</span><span class="sxs-lookup"><span data-stu-id="d5fc6-158">Functions</span></span>
* <span data-ttu-id="d5fc6-159">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="d5fc6-159">Stored procedures</span></span>
* <span data-ttu-id="d5fc6-160">トリガー</span><span class="sxs-lookup"><span data-stu-id="d5fc6-160">Triggers</span></span>
* <span data-ttu-id="d5fc6-161">Views</span><span class="sxs-lookup"><span data-stu-id="d5fc6-161">Views</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="d5fc6-162">移行を削除する</span><span class="sxs-lookup"><span data-stu-id="d5fc6-162">Remove a migration</span></span>

<span data-ttu-id="d5fc6-163">移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-163">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="d5fc6-164">最後の移行を削除するには、このコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-164">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```

``` Console
dotnet ef migrations remove
```

<span data-ttu-id="d5fc6-165">移行の削除後、追加のモデル変更を行い、もう一度追加できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-165">After removing the migration, you can make the additional model changes and add it again.</span></span>

## <a name="revert-a-migration"></a><span data-ttu-id="d5fc6-166">移行を元に戻す</span><span class="sxs-lookup"><span data-stu-id="d5fc6-166">Revert a migration</span></span>

<span data-ttu-id="d5fc6-167">移行をデータベースに既に適用しているが、元に戻す必要がある場合、同じコマンドを使用して移行を適用できますが、ロールバックする移行の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-167">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```

``` Console
dotnet ef database update LastGoodMigration
```

## <a name="generate-sql-scripts"></a><span data-ttu-id="d5fc6-168">SQL スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="d5fc6-168">Generate SQL scripts</span></span>

<span data-ttu-id="d5fc6-169">移行をデバッグするか、それを実稼働データベースに展開するとき、SQL スクリプトを生成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-169">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="d5fc6-170">このスクリプトはさらに見直して正しいかどうかを確認し、実稼働データベースのニーズに合わせて調整できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-170">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="d5fc6-171">このスクリプトは、展開テクノロジとの連動でも利用できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-171">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="d5fc6-172">基本コマンドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-172">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```

``` Console
dotnet ef migrations script
```

<span data-ttu-id="d5fc6-173">このコマンドにはいくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-173">There are several options to this command.</span></span>

<span data-ttu-id="d5fc6-174">**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-174">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="d5fc6-175">移行が適用されていない場合、`0` を指定します (これは既定です)。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-175">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="d5fc6-176">**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-176">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="d5fc6-177">これは既定でプロジェクトの最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-177">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="d5fc6-178">**idempotent** スクリプトをオプションで生成できます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-178">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="d5fc6-179">このスクリプトは、データベースにまだ適用されていない移行のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-179">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="d5fc6-180">これは、データベースに適用された最後の移行が正確にわからない場合、あるいは複数のデータベースに展開するとき、いずれも移行が異なる可能性がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-180">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="d5fc6-181">実行時に移行を適用する</span><span class="sxs-lookup"><span data-stu-id="d5fc6-181">Apply migrations at runtime</span></span>

<span data-ttu-id="d5fc6-182">起動中または最初の実行中、実行時に移行を適用するアプリがあります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-182">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="d5fc6-183">`Migrate()` メソッドを使用してこれを行います。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-183">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="d5fc6-184">このメソッドは、より高度なシナリオで利用される `IMigrator` サービスの上でビルドされます。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-184">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="d5fc6-185">アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-185">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * <span data-ttu-id="d5fc6-186">この方法は万人向けではありません。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-186">This approach isn't for everyone.</span></span> <span data-ttu-id="d5fc6-187">ローカル データベースを利用するアプリには最適ですが、ほとんどのアプリケーションでは、SQL スクリプトの生成など、より堅牢な展開戦略が必要になります。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-187">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="d5fc6-188">`Migrate()` の前に `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-188">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="d5fc6-189">`EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-189">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d5fc6-190">次の手順</span><span class="sxs-lookup"><span data-stu-id="d5fc6-190">Next steps</span></span>

<span data-ttu-id="d5fc6-191">詳細については、<xref:core/miscellaneous/cli/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d5fc6-191">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
