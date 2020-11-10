---
title: 移行の適用-EF Core
description: Entity Framework Core を使用して、運用データベースと開発データベースにスキーマの移行を適用する方法
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429853"
---
# <a name="applying-migrations"></a><span data-ttu-id="3a819-103">移行の適用</span><span class="sxs-lookup"><span data-stu-id="3a819-103">Applying Migrations</span></span>

<span data-ttu-id="3a819-104">移行を追加したら、それらをデプロイしてデータベースに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="3a819-105">これを行うためのさまざまな方法があります。運用環境に適しているものと、開発ライフサイクルのものがあります。</span><span class="sxs-lookup"><span data-stu-id="3a819-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="3a819-106">配置戦略にかかわらず、実稼働データベースに適用する前に、生成された移行を常に検査してテストします。</span><span class="sxs-lookup"><span data-stu-id="3a819-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="3a819-107">移行では、列の名前を変更しようとしたとき、またはデータベースに適用するさまざまな理由で失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="3a819-108">SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="3a819-108">SQL scripts</span></span>

<span data-ttu-id="3a819-109">運用データベースへの移行を展開するには、SQL スクリプトを生成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3a819-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="3a819-110">この方法には、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="3a819-111">SQL スクリプトの精度を確認できます。これは、実稼働データベースにスキーマの変更を適用すると、データの損失を伴う可能性がある危険性の高い操作であるため、重要です。</span><span class="sxs-lookup"><span data-stu-id="3a819-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="3a819-112">場合によっては、実稼働データベースの特定のニーズに合わせてスクリプトをチューニングできます。</span><span class="sxs-lookup"><span data-stu-id="3a819-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="3a819-113">SQL スクリプトは、配置テクノロジと組み合わせて使用することも、CI プロセスの一部として生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="3a819-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="3a819-114">SQL スクリプトは DBA に提供でき、個別に管理およびアーカイブできます。</span><span class="sxs-lookup"><span data-stu-id="3a819-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3a819-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a819-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="3a819-116">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="3a819-116">Basic Usage</span></span>

<span data-ttu-id="3a819-117">次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a819-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="3a819-118">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="3a819-118">With From (to implied)</span></span>

<span data-ttu-id="3a819-119">次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a819-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="3a819-120">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="3a819-120">With From and To</span></span>

<span data-ttu-id="3a819-121">次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。</span><span class="sxs-lookup"><span data-stu-id="3a819-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="3a819-122">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="3a819-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="3a819-123">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="3a819-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a819-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="3a819-125">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="3a819-125">Basic Usage</span></span>

<span data-ttu-id="3a819-126">次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a819-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="3a819-127">From を使用する (暗黙的な指定に対して)</span><span class="sxs-lookup"><span data-stu-id="3a819-127">With From (to implied)</span></span>

<span data-ttu-id="3a819-128">次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a819-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="3a819-129">From および To を使用する</span><span class="sxs-lookup"><span data-stu-id="3a819-129">With From and To</span></span>

<span data-ttu-id="3a819-130">次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。</span><span class="sxs-lookup"><span data-stu-id="3a819-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```

<span data-ttu-id="3a819-131">ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="3a819-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="3a819-132">*データ損失の可能性のあるシナリオには注意してください。*</span><span class="sxs-lookup"><span data-stu-id="3a819-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="3a819-133">スクリプトの生成では、次の2つの引数を使用して、どの移行範囲を生成する必要があるかを示します。</span><span class="sxs-lookup"><span data-stu-id="3a819-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="3a819-134">**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="3a819-135">移行が適用されていない場合、`0` を指定します (これは既定です)。</span><span class="sxs-lookup"><span data-stu-id="3a819-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="3a819-136">**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。</span><span class="sxs-lookup"><span data-stu-id="3a819-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="3a819-137">これは既定でプロジェクトの最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="3a819-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="3a819-138">べき等 SQL スクリプト</span><span class="sxs-lookup"><span data-stu-id="3a819-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="3a819-139">上記で生成した SQL スクリプトは、ある移行から別の移行にスキーマを変更する場合にのみ適用できます。スクリプトは適切に適用する必要があり、適切な移行状態のデータベースのみに適用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3a819-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="3a819-140">EF Core では、 **べき等** スクリプトの生成もサポートしています。このスクリプトでは、(移行履歴テーブルを使用して) 既に適用されている移行を内部で確認し、不足しているもののみを適用します</span><span class="sxs-lookup"><span data-stu-id="3a819-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="3a819-141">これは、データベースに最後に適用された移行の内容を正確に把握していない場合、またはそれぞれが異なる移行に配置されている複数のデータベースに配置する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="3a819-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="3a819-142">次の例では、べき等移行が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3a819-142">The following generates idempotent migrations:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3a819-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a819-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[<span data-ttu-id="3a819-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a819-144">Visual Studio</span></span>](#tab/vs)

```powershell
Script-Migration -Idempotent
```

<span data-ttu-id="3a819-145">\*\*_</span><span class="sxs-lookup"><span data-stu-id="3a819-145">\*\*_</span></span>

## <a name="command-line-tools"></a><span data-ttu-id="3a819-146">コマンドライン ツール</span><span class="sxs-lookup"><span data-stu-id="3a819-146">Command-line tools</span></span>

<span data-ttu-id="3a819-147">EF コマンドラインツールを使用すると、データベースに移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="3a819-147">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="3a819-148">移行のローカル開発とテストの生産性を維持しながら、この方法は運用データベースの管理には適していません。</span><span class="sxs-lookup"><span data-stu-id="3a819-148">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

<span data-ttu-id="3a819-149">_ SQL コマンドはツールによって直接適用されるので、開発者はそれを検査または変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a819-149">_ The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="3a819-150">これは、運用環境では危険である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-150">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="3a819-151">.NET SDK と EF ツールは、実稼働サーバーにインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-151">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3a819-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a819-152">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="3a819-153">次の例では、データベースを最新の移行に更新します。</span><span class="sxs-lookup"><span data-stu-id="3a819-153">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="3a819-154">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3a819-154">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="3a819-155">これは、以前の移行にもロールバックするために使用できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-155">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="3a819-156">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-156">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="3a819-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a819-157">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="3a819-158">次の例では、データベースを最新の移行に更新します。</span><span class="sxs-lookup"><span data-stu-id="3a819-158">The following updates your database to the latest migration:</span></span>

```powershell
Update-Database
```

<span data-ttu-id="3a819-159">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3a819-159">The following updates your database to a given migration:</span></span>

```powershell
Update-Database AddNewTables
```

<span data-ttu-id="3a819-160">これは、以前の移行にもロールバックするために使用できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-160">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="3a819-161">データ損失の可能性のあるシナリオには注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-161">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="3a819-162">コマンドラインツールを使用した移行の適用の詳細については、「 [EF Core ツールリファレンス](xref:core/cli/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-162">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="3a819-163">実行時に移行を適用する</span><span class="sxs-lookup"><span data-stu-id="3a819-163">Apply migrations at runtime</span></span>

<span data-ttu-id="3a819-164">アプリケーション自体が、通常は起動時にプログラムを使用して移行を適用できる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-164">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="3a819-165">移行のローカル開発とテストの生産性を維持しながら、次のような理由から、この方法は実稼働データベースの管理には適していません。</span><span class="sxs-lookup"><span data-stu-id="3a819-165">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="3a819-166">アプリケーションの複数のインスタンスが実行されている場合、両方のアプリケーションが移行を同時に適用して失敗する (または、データの破損の原因となる) ことがあります。</span><span class="sxs-lookup"><span data-stu-id="3a819-166">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="3a819-167">同様に、別のアプリケーションが移行している間にアプリケーションがデータベースにアクセスしている場合は、重大な問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-167">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="3a819-168">データベーススキーマを変更するには、アプリケーションに昇格されたアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="3a819-168">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="3a819-169">一般に、運用環境でアプリケーションのデータベースのアクセス許可を制限することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3a819-169">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="3a819-170">問題が発生した場合は、適用された移行をロールバックできることが重要です。</span><span class="sxs-lookup"><span data-stu-id="3a819-170">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="3a819-171">その他の戦略では、簡単にそのまま使用できます。</span><span class="sxs-lookup"><span data-stu-id="3a819-171">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="3a819-172">SQL コマンドはプログラムによって直接適用されるので、開発者はそれを検査または変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a819-172">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="3a819-173">これは、運用環境では危険である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a819-173">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="3a819-174">プログラムによって移行を適用するには、を呼び出し `context.Database.Migrate()` ます。</span><span class="sxs-lookup"><span data-stu-id="3a819-174">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="3a819-175">たとえば、一般的な ASP.NET アプリケーションでは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="3a819-175">For example, a typical ASP.NET application can do the following:</span></span>

```csharp
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

<span data-ttu-id="3a819-176">は、 `Migrate()` `IMigrator` より高度なシナリオで使用できるサービスの上に構築されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-176">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="3a819-177">アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。</span><span class="sxs-lookup"><span data-stu-id="3a819-177">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="3a819-178">実稼働環境でこの方法を使用する前に、慎重に検討してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-178">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="3a819-179">この展開戦略の単純化は、作成される問題によって上回るされていることがわかりました。</span><span class="sxs-lookup"><span data-stu-id="3a819-179">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="3a819-180">代わりに、移行から SQL スクリプトを生成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="3a819-180">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="3a819-181">`Migrate()` の前に `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="3a819-181">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="3a819-182">`EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。</span><span class="sxs-lookup"><span data-stu-id="3a819-182">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
