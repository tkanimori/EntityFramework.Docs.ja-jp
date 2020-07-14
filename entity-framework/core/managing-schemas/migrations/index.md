---
title: 移行の概要 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238230"
---
# <a name="migrations-overview"></a><span data-ttu-id="7e418-102">移行の概要</span><span class="sxs-lookup"><span data-stu-id="7e418-102">Migrations Overview</span></span>

<span data-ttu-id="7e418-103">実プロジェクトでは、機能が実装されると、データ モデルが変更されます。新しいエンティティまたはプロパティが追加および削除されると、アプリケーションと一致させるために、データベース スキーマを変更しなければならなくなります。</span><span class="sxs-lookup"><span data-stu-id="7e418-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="7e418-104">EF Core の移行機能では、データベースの既存のデータを維持しながら、アプリケーションのデータ モデルとデータベース スキーマを同期した状態で、データベース スキーマを増分的に更新することができます。</span><span class="sxs-lookup"><span data-stu-id="7e418-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="7e418-105">移行は、大まかに次のように機能します。</span><span class="sxs-lookup"><span data-stu-id="7e418-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="7e418-106">データ モデルの変更が採用されると、データベース スキーマを一致させておくために、必要な更新が記述された対応する移行を、開発者は EF Core ツールを使用して追加します。EF Core は、現在のモデルを古いモデルのスナップショットと比較して相違点を特定し、移行ソース ファイルを生成します。このファイルは、他のソース ファイルと同様に、ご自分のプロジェクトのソース管理で追跡できます。</span><span class="sxs-lookup"><span data-stu-id="7e418-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="7e418-107">新しい移行が生成されると、それをさまざまな方法でデータベースに適用できます。</span><span class="sxs-lookup"><span data-stu-id="7e418-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="7e418-108">EF Core では、適用されたすべての移行を特別な履歴テーブルに記録します。これにより、どの移行が適用されたか、またはされていないかを知ることができます。</span><span class="sxs-lookup"><span data-stu-id="7e418-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="7e418-109">このページの残りの部分では、移行手順を初心者向けに詳細に説明します。</span><span class="sxs-lookup"><span data-stu-id="7e418-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="7e418-110">さらなる詳細については、このセクションの他のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e418-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="7e418-111">作業の開始</span><span class="sxs-lookup"><span data-stu-id="7e418-111">Getting started</span></span>

<span data-ttu-id="7e418-112">次の単純なモデルが含まれる、あなたの初めての EF Core アプリケーションが完成したとします。</span><span class="sxs-lookup"><span data-stu-id="7e418-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="7e418-113">開発時に、[Create API と Drop API](xref:core/managing-schemas/ensure-created) を使用して、すばやく反復処理を行い、ご自分のモデルに必要に応じた変更を加えたとします。しかし、今度ご自分のアプリケーションが運用環境に移行することになったので、データベース全体を削除せず、スキーマを安全に発展させる方法が必要です。</span><span class="sxs-lookup"><span data-stu-id="7e418-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="7e418-114">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="7e418-114">Install the tools</span></span>

<span data-ttu-id="7e418-115">まず、[EF Core のコマンドライン ツール](xref:core/miscellaneous/cli/index)をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e418-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="7e418-116">一般的に、どのプラットフォームでも動作する [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet)の使用が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7e418-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="7e418-117">Visual Studio 内で作業するのに慣れている場合や、EF6 での移行経験がある場合は、[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7e418-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="7e418-118">ご自分の最初の移行を作成する</span><span class="sxs-lookup"><span data-stu-id="7e418-118">Create your first migration</span></span>

<span data-ttu-id="7e418-119">これで、ご自分の最初の移行を追加する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="7e418-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="7e418-120">EF Core に **InitialCreate**という名前の移行を作成するように指示します。</span><span class="sxs-lookup"><span data-stu-id="7e418-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7e418-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e418-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="7e418-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e418-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="7e418-123">EF Core により、ご自分のプロジェクトに **Migrations** というディレクトリが作成され、いくつかのファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7e418-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="7e418-124">EF Core で生成された内容を確認し、場合によっては修正することが推奨されますが、ここではそれは省きます。</span><span class="sxs-lookup"><span data-stu-id="7e418-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="7e418-125">ご自分のデータベースとスキーマを作成する</span><span class="sxs-lookup"><span data-stu-id="7e418-125">Create your database and schema</span></span>

<span data-ttu-id="7e418-126">この時点で、EF にご自分のデータベースを作成させ、移行からご自分のスキーマを作成できます。</span><span class="sxs-lookup"><span data-stu-id="7e418-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="7e418-127">これは、次のように行います。</span><span class="sxs-lookup"><span data-stu-id="7e418-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7e418-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e418-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="7e418-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e418-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="7e418-130">これで完了です。SQL を 1 行も記述せずに、ご自分のアプリケーションをご自分の新しいデータベースで実行する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="7e418-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="7e418-131">この方法での移行の適用は、ローカルでの開発には適していますが、運用環境には適していません。詳細については、[移行の適用](xref:core/managing-schemas/migrations/applying)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e418-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="7e418-132">ご自分のモデルを発展させる</span><span class="sxs-lookup"><span data-stu-id="7e418-132">Evolving your model</span></span>

<span data-ttu-id="7e418-133">数日後、ご自分のブログに作成のタイムスタンプを追加するように求められました。</span><span class="sxs-lookup"><span data-stu-id="7e418-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="7e418-134">ご自分のアプリケーションに必要な変更を行ったので、現在、ご自分のモデルは次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="7e418-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="7e418-135">現在、ご自分のモデルとご自分の実稼働データベースは一致しなくなっています。ご自分のデータベース スキーマに新しい列を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e418-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="7e418-136">このために、新しい移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="7e418-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7e418-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e418-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="7e418-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e418-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="7e418-139">移行には、後でプロジェクト履歴がわかりやすいように、わかりやすい名前が付いていることに着目してください。</span><span class="sxs-lookup"><span data-stu-id="7e418-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="7e418-140">これはプロジェクトの最初の移行ではないため、EF Core では、列の追加前に、ご自分の更新されたモデルを古いモデルのスナップショットと比較します。このモデルのスナップショットとは、移行を追加したときに EF Core によって生成されるファイルの 1 つであり、ソース管理にチェックインされます。</span><span class="sxs-lookup"><span data-stu-id="7e418-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="7e418-141">その比較に基づき、EF Core は列が追加されたことを検出し、適切な移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7e418-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="7e418-142">これで、以前と同じようにご自分の移行を適用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7e418-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7e418-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e418-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="7e418-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e418-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="7e418-145">今回は、データベースが既に存在していることが EF によって検出されていることに着目してください。</span><span class="sxs-lookup"><span data-stu-id="7e418-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="7e418-146">また、上記の最初の移行が適用されたとき、その事実がご自分のデータベースの特別な移行履歴テーブルに記録されました。これにより、EF は新しい移行のみを自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="7e418-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="7e418-147">次の手順</span><span class="sxs-lookup"><span data-stu-id="7e418-147">Next steps</span></span>

<span data-ttu-id="7e418-148">上では、移行について簡単に説明しました。</span><span class="sxs-lookup"><span data-stu-id="7e418-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="7e418-149">[移行の管理](xref:core/managing-schemas/migrations/managing)および[それの適用](xref:core/managing-schemas/migrations/applying)などについては、その他のドキュメント ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e418-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="7e418-150">[.NET Core CLI ツール リファレンス](xref:core/miscellaneous/cli/index)にも、さまざまなコマンドに関する有用な情報があります。</span><span class="sxs-lookup"><span data-stu-id="7e418-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
