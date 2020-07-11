---
title: 移行の適用-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238727"
---
# <a name="applying-migrations"></a><span data-ttu-id="83e9a-102">移行の適用</span><span class="sxs-lookup"><span data-stu-id="83e9a-102">Applying Migrations</span></span>

<span data-ttu-id="83e9a-103">移行を追加したら、それらをデプロイしてデータベースに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="83e9a-104">これを行うためのさまざまな方法があります。運用環境に適しているものと、開発ライフサイクルのものがあります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="83e9a-105">配置戦略にかかわらず、実稼働データベースに適用する前に、生成された移行を常に検査してテストします。</span><span class="sxs-lookup"><span data-stu-id="83e9a-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="83e9a-106">移行では、列の名前を変更しようとしたとき、またはデータベースに適用するさまざまな理由で失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="83e9a-107">SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="83e9a-107">SQL scripts</span></span>

<span data-ttu-id="83e9a-108">運用データベースへの移行を展開するには、SQL スクリプトを生成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83e9a-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="83e9a-109">この方法には、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="83e9a-110">SQL スクリプトの精度を確認できます。これは、実稼働データベースにスキーマの変更を適用すると、データの損失を伴う可能性がある危険性の高い操作であるため、重要です。</span><span class="sxs-lookup"><span data-stu-id="83e9a-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="83e9a-111">場合によっては、実稼働データベースの特定のニーズに合わせてスクリプトをチューニングできます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="83e9a-112">SQL スクリプトは、配置テクノロジと組み合わせて使用することも、CI プロセスの一部として生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="83e9a-113">SQL スクリプトは DBA に提供でき、個別に管理およびアーカイブできます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="83e9a-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="83e9a-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="83e9a-115">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="83e9a-115">Basic Usage</span></span>

<span data-ttu-id="83e9a-116">次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="83e9a-117">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="83e9a-117">With From (to implied)</span></span>

<span data-ttu-id="83e9a-118">次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="83e9a-119">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="83e9a-119">With From and To</span></span>

<span data-ttu-id="83e9a-120">次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="83e9a-121">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="83e9a-122">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="83e9a-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83e9a-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="83e9a-124">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="83e9a-124">Basic Usage</span></span>

<span data-ttu-id="83e9a-125">次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="83e9a-126">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="83e9a-126">With From (to implied)</span></span>

<span data-ttu-id="83e9a-127">次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="83e9a-128">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="83e9a-128">With From and To</span></span>

<span data-ttu-id="83e9a-129">次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="83e9a-130">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="83e9a-131">*データ損失の可能性のあるシナリオには注意してください。*</span><span class="sxs-lookup"><span data-stu-id="83e9a-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="83e9a-132">スクリプトの生成では、次の2つの引数を使用して、どの移行範囲を生成する必要があるかを示します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="83e9a-133">**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="83e9a-134">移行が適用されていない場合、`0` を指定します (これは既定です)。</span><span class="sxs-lookup"><span data-stu-id="83e9a-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="83e9a-135">**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。</span><span class="sxs-lookup"><span data-stu-id="83e9a-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="83e9a-136">これは既定でプロジェクトの最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="83e9a-137">べき等 SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="83e9a-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="83e9a-138">上記で生成した SQL スクリプトは、ある移行から別の移行にスキーマを変更する場合にのみ適用できます。スクリプトは適切に適用する必要があり、適切な移行状態のデータベースのみに適用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83e9a-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="83e9a-139">EF Core では、**べき等**スクリプトの生成もサポートしています。このスクリプトでは、(移行履歴テーブルを使用して) 既に適用されている移行を内部で確認し、不足しているもののみを適用します</span><span class="sxs-lookup"><span data-stu-id="83e9a-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="83e9a-140">これは、データベースに最後に適用された移行の内容を正確に把握していない場合、またはそれぞれが異なる移行に配置されている複数のデータベースに配置する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="83e9a-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="83e9a-141">次の例では、べき等移行が生成されます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="83e9a-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="83e9a-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="83e9a-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83e9a-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="83e9a-144">コマンドライン ツール</span><span class="sxs-lookup"><span data-stu-id="83e9a-144">Command-line tools</span></span>

<span data-ttu-id="83e9a-145">EF コマンドラインツールを使用すると、データベースに移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="83e9a-146">移行のローカル開発とテストの生産性を維持しながら、この方法は運用データベースの管理には適していません。</span><span class="sxs-lookup"><span data-stu-id="83e9a-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="83e9a-147">SQL コマンドはツールによって直接適用されます。開発者は、これらのコマンドを検査または変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="83e9a-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="83e9a-148">これは、運用環境では危険である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="83e9a-149">.NET SDK と EF ツールは、実稼働サーバーにインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="83e9a-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="83e9a-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="83e9a-151">次の例では、データベースを最新の移行に更新します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="83e9a-152">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="83e9a-153">これは、以前の移行にもロールバックするために使用できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="83e9a-154">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="83e9a-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83e9a-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="83e9a-156">次の例では、データベースを最新の移行に更新します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="83e9a-157">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="83e9a-158">これは、以前の移行にもロールバックするために使用できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="83e9a-159">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="83e9a-160">コマンドラインツールを使用した移行の適用の詳細については、「 [EF Core ツールリファレンス](xref:core/miscellaneous/cli/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="83e9a-161">実行時に移行を適用する</span><span class="sxs-lookup"><span data-stu-id="83e9a-161">Apply migrations at runtime</span></span>

<span data-ttu-id="83e9a-162">アプリケーション自体が、通常は起動時にプログラムを使用して移行を適用できる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="83e9a-163">移行のローカル開発とテストの生産性を維持しながら、次のような理由から、この方法は実稼働データベースの管理には適していません。</span><span class="sxs-lookup"><span data-stu-id="83e9a-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="83e9a-164">アプリケーションの複数のインスタンスが実行されている場合、両方のアプリケーションが移行を同時に適用して失敗する (または、データの破損の原因となる) ことがあります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="83e9a-165">同様に、別のアプリケーションが移行している間にアプリケーションがデータベースにアクセスしている場合は、重大な問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="83e9a-166">データベーススキーマを変更するには、アプリケーションに昇格されたアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="83e9a-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="83e9a-167">一般に、運用環境でアプリケーションのデータベースのアクセス許可を制限することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83e9a-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="83e9a-168">問題が発生した場合は、適用された移行をロールバックできることが重要です。</span><span class="sxs-lookup"><span data-stu-id="83e9a-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="83e9a-169">その他の戦略では、簡単にそのまま使用できます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="83e9a-170">SQL コマンドはプログラムによって直接適用されます。開発者は、これらのコマンドを検査または変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="83e9a-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="83e9a-171">これは、運用環境では危険である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="83e9a-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="83e9a-172">プログラムによって移行を適用するには、を呼び出し `context.Database.Migrate()` ます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="83e9a-173">たとえば、一般的な ASP.NET アプリケーションでは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="83e9a-173">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="83e9a-174">は、 `Migrate()` `IMigrator` より高度なシナリオで使用できるサービスの上に構築されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="83e9a-175">アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="83e9a-176">実稼働環境でこの方法を使用する前に、慎重に検討してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="83e9a-177">この展開戦略の単純化は、作成される問題によって上回るされていることがわかりました。</span><span class="sxs-lookup"><span data-stu-id="83e9a-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="83e9a-178">代わりに SQL スクリプトを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="83e9a-179">`Migrate()` の前に `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="83e9a-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="83e9a-180">`EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。</span><span class="sxs-lookup"><span data-stu-id="83e9a-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
