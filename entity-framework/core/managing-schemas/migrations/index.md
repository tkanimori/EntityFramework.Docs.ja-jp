---
title: 移行 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 4a5d6f3798c7af7597f95cebea1aeb9e5e58d277
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996523"
---
<a name="migrations"></a><span data-ttu-id="4ba1f-102">移行</span><span class="sxs-lookup"><span data-stu-id="4ba1f-102">Migrations</span></span>
==========
<span data-ttu-id="4ba1f-103">移行とはデータベースに対するスキーマ変更の適用を増分的に増やす方法であり、データベースの既存データを維持しながら、EF Core モデルと同期します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-103">Migrations provide a way to incrementally apply schema changes to the database to keep it in sync with your EF Core model while preserving existing data in the database.</span></span>

<a name="creating-the-database"></a><span data-ttu-id="4ba1f-104">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="4ba1f-104">Creating the database</span></span>
---------------------
<span data-ttu-id="4ba1f-105">[最初のモデルを定義][1]したら、次にデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-105">After you've [defined your initial model][1], it's time to create the database.</span></span> <span data-ttu-id="4ba1f-106">これを行うには、初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-106">To do this, add an initial migration.</span></span>
<span data-ttu-id="4ba1f-107">[EF Core ツール][2]をインストールし、適切なコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-107">Install the [EF Core Tools][2] and run the appropriate command.</span></span>

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="4ba1f-108">**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-108">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="4ba1f-109">**00000000000000_InitialCreate.cs**--メインの移行ファイル。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-109">**00000000000000_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="4ba1f-110">(`Up()` で) 移行を適用し、(`Down()` で) それを元に戻すために必要な操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-110">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="4ba1f-111">**00000000000000_InitialCreate.Designer.cs**--移行メタデータ ファイル。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-111">**00000000000000_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="4ba1f-112">EF によって使用される情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-112">Contains information used by EF.</span></span>
* <span data-ttu-id="4ba1f-113">**MyContextModelSnapshot.cs**--現在のモデルのスナップショット。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-113">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="4ba1f-114">次の移行を追加するときの変更内容の決定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-114">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="4ba1f-115">変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-115">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="4ba1f-116">移行ファイルは自由に移動したり、その名前空間を変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-116">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="4ba1f-117">新しい移行は前回の移行の兄弟として作成されます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-117">New migrations are created as siblings of the last migration.</span></span>

<span data-ttu-id="4ba1f-118">次に、移行をデータベースに適用し、スキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-118">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="adding-another-migration"></a><span data-ttu-id="4ba1f-119">別の移行を追加する</span><span class="sxs-lookup"><span data-stu-id="4ba1f-119">Adding another migration</span></span>
------------------------
<span data-ttu-id="4ba1f-120">EF Core モデルの変更後、データベース スキーマは同期していない状態になります。それを最新の状態にするには、別の移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-120">After making changes to your EF Core model, the database schema will be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="4ba1f-121">移行名は、バージョン管理システムのコミット メッセージのように使用できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-121">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="4ba1f-122">たとえば、製品の顧客レビューを変更して保存した場合、*AddProductReviews* のようなものを選択できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-122">For example, if I made changes to save customer reviews of products, I might choose something like *AddProductReviews*.</span></span>

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="4ba1f-123">移行のスキャフォールディング後、それが正確であるか確認し、追加の操作が必要であればそれを追加し、正しく適用します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-123">Once the migration is scaffolded, you should review it for accuracy and add any additional operations required to apply it correctly.</span></span> <span data-ttu-id="4ba1f-124">たとえば、移行には次の操作が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-124">For example, your migration might contain the following operations:</span></span>

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

<span data-ttu-id="4ba1f-125">これらの操作はデータベース スキーマに互換性を与えますが、既存の顧客名を保持しません。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-125">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="4ba1f-126">改善するには、次のように書き直します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-126">To make it better, rewrite it as follows.</span></span>

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
> <span data-ttu-id="4ba1f-127">データ損失 (列の脱落など) を引き起こす可能性のある操作がスキャフォールディングされるときは、新しい移行を追加すると警告が出ます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-127">Adding a new migration warns when an operation is scaffolded that may result in data loss (like dropping a column).</span></span> <span data-ttu-id="4ba1f-128">このような移行の場合は特に正しいかどうかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-128">Be sure to especially review these migrations for accuracy.</span></span>

<span data-ttu-id="4ba1f-129">適切なコマンドを利用し、データベースに移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-129">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="removing-a-migration"></a><span data-ttu-id="4ba1f-130">移行を削除する</span><span class="sxs-lookup"><span data-stu-id="4ba1f-130">Removing a migration</span></span>
--------------------
<span data-ttu-id="4ba1f-131">移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-131">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span>
<span data-ttu-id="4ba1f-132">最後の移行を削除するには、このコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-132">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

<span data-ttu-id="4ba1f-133">削除後、追加のモデル変更を行い、もう一度追加できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-133">After removing it, you can make the additional model changes and add it again.</span></span>

<a name="reverting-a-migration"></a><span data-ttu-id="4ba1f-134">移行を元に戻す</span><span class="sxs-lookup"><span data-stu-id="4ba1f-134">Reverting a migration</span></span>
---------------------
<span data-ttu-id="4ba1f-135">移行をデータベースに既に適用しているが、元に戻す必要がある場合、同じコマンドを使用して移行を適用できますが、ロールバックする移行の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-135">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="empty-migrations"></a><span data-ttu-id="4ba1f-136">空の移行</span><span class="sxs-lookup"><span data-stu-id="4ba1f-136">Empty migrations</span></span>
----------------
<span data-ttu-id="4ba1f-137">モデル変更を行わずに移行を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-137">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="4ba1f-138">その場合、新しい移行を追加すると空の移行が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-138">In this case, adding a new migration creates an empty one.</span></span> <span data-ttu-id="4ba1f-139">EF Core モデルに直接関連しない操作を実行するようにこの移行をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-139">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span>
<span data-ttu-id="4ba1f-140">この方法で管理すると便利なものは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-140">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="4ba1f-141">フルテキスト検索</span><span class="sxs-lookup"><span data-stu-id="4ba1f-141">Full-Text Search</span></span>
* <span data-ttu-id="4ba1f-142">関数</span><span class="sxs-lookup"><span data-stu-id="4ba1f-142">Functions</span></span>
* <span data-ttu-id="4ba1f-143">ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="4ba1f-143">Stored procedures</span></span>
* <span data-ttu-id="4ba1f-144">トリガー</span><span class="sxs-lookup"><span data-stu-id="4ba1f-144">Triggers</span></span>
* <span data-ttu-id="4ba1f-145">Views</span><span class="sxs-lookup"><span data-stu-id="4ba1f-145">Views</span></span>
* <span data-ttu-id="4ba1f-146">その他</span><span class="sxs-lookup"><span data-stu-id="4ba1f-146">etc.</span></span>

<a name="generating-a-sql-script"></a><span data-ttu-id="4ba1f-147">SQL スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="4ba1f-147">Generating a SQL script</span></span>
-----------------------
<span data-ttu-id="4ba1f-148">移行をデバッグするか、それを実稼働データベースに展開するとき、SQL スクリプトを生成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-148">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="4ba1f-149">このスクリプトはさらに見直して正しいかどうかを確認し、実稼働データベースのニーズに合わせて調整できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-149">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="4ba1f-150">このスクリプトは、展開テクノロジとの連動でも利用できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-150">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="4ba1f-151">基本コマンドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-151">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

<span data-ttu-id="4ba1f-152">このコマンドにはいくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-152">There are several options to this command.</span></span>

<span data-ttu-id="4ba1f-153">**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-153">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="4ba1f-154">移行が適用されていない場合、`0` を指定します (これは既定です)。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-154">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="4ba1f-155">**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-155">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="4ba1f-156">これは既定でプロジェクトの最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-156">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="4ba1f-157">**idempotent** スクリプトをオプションで生成できます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-157">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="4ba1f-158">このスクリプトは、データベースにまだ適用されていない移行のみを適用します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-158">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="4ba1f-159">これは、データベースに適用された最後の移行が正確にわからない場合、あるいは複数のデータベースに展開するとき、いずれも移行が異なる可能性がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-159">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<a name="applying-migrations-at-runtime"></a><span data-ttu-id="4ba1f-160">実行時に移行を適用する</span><span class="sxs-lookup"><span data-stu-id="4ba1f-160">Applying migrations at runtime</span></span>
------------------------------
<span data-ttu-id="4ba1f-161">起動中または最初の実行中、実行時に移行を適用するアプリがあります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-161">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="4ba1f-162">`Migrate()` メソッドを使用してこれを行います。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-162">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="4ba1f-163">この方法は万人向けではないため、注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-163">Caution, this approach isn't for everyone.</span></span> <span data-ttu-id="4ba1f-164">ローカル データベースを利用するアプリには最適ですが、ほとんどのアプリケーションでは、SQL スクリプトの生成など、より堅牢な展開戦略が必要になります。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-164">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> <span data-ttu-id="4ba1f-165">`Migrate()` の前に `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-165">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="4ba1f-166">`EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-166">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

> [!NOTE]
> <span data-ttu-id="4ba1f-167">このメソッドは、より高度なシナリオで利用される `IMigrator` サービスの上でビルドされます。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-167">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="4ba1f-168">アクセスするには `DbContext.GetService<IMigrator>()` を利用します。</span><span class="sxs-lookup"><span data-stu-id="4ba1f-168">Use `DbContext.GetService<IMigrator>()` to access it.</span></span>


  [1]: ../../modeling/index.md
  [2]: ../../miscellaneous/cli/index.md
