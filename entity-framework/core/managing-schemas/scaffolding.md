---
title: リバース エンジニア リング - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: ef729c0c26d5a1f57099f339eb51cda7e83289df
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688681"
---
# <a name="reverse-engineering"></a><span data-ttu-id="04210-102">リバース エンジニア リング</span><span class="sxs-lookup"><span data-stu-id="04210-102">Reverse Engineering</span></span>

<span data-ttu-id="04210-103">リバース エンジニア リングは、エンティティ型クラスとデータベース スキーマに基づいて DbContext クラスをスキャフォールディングのプロセスです。</span><span class="sxs-lookup"><span data-stu-id="04210-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="04210-104">使用して、実行できる、 `Scaffold-DbContext` EF Core パッケージ マネージャー コンソール (PMC) ツールのコマンドまたは`dotnet ef dbcontext scaffold`.NET コマンド ライン インターフェイス (CLI) ツールのコマンド。</span><span class="sxs-lookup"><span data-stu-id="04210-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="04210-105">インストール</span><span class="sxs-lookup"><span data-stu-id="04210-105">Installing</span></span>

<span data-ttu-id="04210-106">リバース エンジニア リング、前に、いずれかをインストールする必要があります、 [PMC ツール](xref:core/miscellaneous/cli/powershell)(Visual Studio のみ) または[CLI ツール](xref:core/miscellaneous/cli/dotnet)します。</span><span class="sxs-lookup"><span data-stu-id="04210-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="04210-107">詳細については、リンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="04210-107">See links for details.</span></span>

<span data-ttu-id="04210-108">適切なインストールも必要[データベース プロバイダー](xref:core/providers/index)データベース スキーマをリバース エンジニア リングします。</span><span class="sxs-lookup"><span data-stu-id="04210-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="04210-109">接続文字列</span><span class="sxs-lookup"><span data-stu-id="04210-109">Connection string</span></span>

<span data-ttu-id="04210-110">コマンドの最初の引数は、データベースに接続文字列です。</span><span class="sxs-lookup"><span data-stu-id="04210-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="04210-111">ツールでは、この接続文字列を使用して、データベース スキーマを読み取る。</span><span class="sxs-lookup"><span data-stu-id="04210-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="04210-112">引用符で囲むし、接続文字列をエスケープする方法は、コマンドの実行に使用しているどのシェルに依存します。</span><span class="sxs-lookup"><span data-stu-id="04210-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="04210-113">詳細については、シェルのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="04210-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="04210-114">PowerShell をエスケープするために必要ななど、`$`しない場合は、文字`\`します。</span><span class="sxs-lookup"><span data-stu-id="04210-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="04210-115">構成とユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="04210-115">Configuration and User Secrets</span></span>

<span data-ttu-id="04210-116">使用することができます、ASP.NET Core プロジェクトがあれば、`Name=<connection-string>`構成から接続文字列を確認するための構文。</span><span class="sxs-lookup"><span data-stu-id="04210-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="04210-117">これは連動、 [Secret Manager ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)コードベースからデータベースのパスワードを分離します。</span><span class="sxs-lookup"><span data-stu-id="04210-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="04210-118">プロバイダー名</span><span class="sxs-lookup"><span data-stu-id="04210-118">Provider name</span></span>

<span data-ttu-id="04210-119">2 番目の引数は、プロバイダーの名前です。</span><span class="sxs-lookup"><span data-stu-id="04210-119">The second argument is the provider name.</span></span> <span data-ttu-id="04210-120">プロバイダー名は、通常プロバイダーの NuGet パッケージの名前と同じです。</span><span class="sxs-lookup"><span data-stu-id="04210-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="04210-121">テーブルを指定します。</span><span class="sxs-lookup"><span data-stu-id="04210-121">Specifying tables</span></span>

<span data-ttu-id="04210-122">データベース スキーマのすべてのテーブルは、既定では、エンティティ型に組み込まれたリバースされます。</span><span class="sxs-lookup"><span data-stu-id="04210-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="04210-123">テーブルは、スキーマとテーブルを指定することでエンジニア リング リバースを制限することができます。</span><span class="sxs-lookup"><span data-stu-id="04210-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="04210-124">`-Schemas` PMC でパラメーターと`--schema`スキーマ内のすべてのテーブルを含めるには、CLI でのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="04210-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="04210-125">`-Tables` (PMC) と`--table`(CLI) を使用して特定のテーブルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="04210-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="04210-126">PMC で複数のテーブルを含めるには、配列を使用します。</span><span class="sxs-lookup"><span data-stu-id="04210-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="04210-127">CLI での複数のテーブルに含めるには、複数回オプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="04210-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="04210-128">名の保存</span><span class="sxs-lookup"><span data-stu-id="04210-128">Preserving names</span></span>

<span data-ttu-id="04210-129">テーブルおよび列名は、既定で型とプロパティの .NET の名前付け規則に合わせて修正しました。</span><span class="sxs-lookup"><span data-stu-id="04210-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="04210-130">指定する、 `-UseDatabaseNames` PMC でスイッチまたは`--use-database-names`CLI でのオプションは、可能な限り、元のデータベース名を保持するこの動作を無効になります。</span><span class="sxs-lookup"><span data-stu-id="04210-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="04210-131">無効な .NET 識別子はまだ修正され、ナビゲーション プロパティと同様に合成された名前は、.NET の名前付け規則に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="04210-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="04210-132">Fluent API や Data Annotations</span><span class="sxs-lookup"><span data-stu-id="04210-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="04210-133">エンティティ型、Fluent API を使用して既定で構成されます。</span><span class="sxs-lookup"><span data-stu-id="04210-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="04210-134">指定`-DataAnnotations`(PMC) または`--data-annotations`可能であれば、データ注釈を使用する (CLI)。</span><span class="sxs-lookup"><span data-stu-id="04210-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="04210-135">たとえば、Fluent API を使用してがスキャフォールディングこのします。</span><span class="sxs-lookup"><span data-stu-id="04210-135">For example, using the Fluent API will scaffold the this.</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="04210-136">データ注釈を使用しているときにスキャフォールディングこれです。</span><span class="sxs-lookup"><span data-stu-id="04210-136">While using Data Annotations will scaffold this.</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="04210-137">DbContext の名前</span><span class="sxs-lookup"><span data-stu-id="04210-137">DbContext name</span></span>

<span data-ttu-id="04210-138">スキャフォールディングの DbContext クラス名のサフィックスが付いたデータベースの名前になります*コンテキスト*既定。</span><span class="sxs-lookup"><span data-stu-id="04210-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="04210-139">使用して、別のアカウントを指定する`-Context`PMC でと`--context`CLI でします。</span><span class="sxs-lookup"><span data-stu-id="04210-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="04210-140">ディレクトリおよび名前空間</span><span class="sxs-lookup"><span data-stu-id="04210-140">Directories and namespaces</span></span>

<span data-ttu-id="04210-141">エンティティ クラスと DbContext クラスは、プロジェクトのルート ディレクトリにスキャフォールディングされ、プロジェクトの既定の名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="04210-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="04210-142">ディレクトリを指定する場所を使用してクラスがスキャフォールディングされる`-OutputDir`(PMC) または`--output-dir`(CLI)。</span><span class="sxs-lookup"><span data-stu-id="04210-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="04210-143">名前空間は、ルート名前空間と、プロジェクトのルート ディレクトリの下の任意のサブディレクトリの名前になります。</span><span class="sxs-lookup"><span data-stu-id="04210-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="04210-144">使用することも`-ContextDir`(PMC) と`--context-dir`エンティティ型のクラスから別のディレクトリに、DbContext クラスをスキャフォールディングする (CLI)。</span><span class="sxs-lookup"><span data-stu-id="04210-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="04210-145">しくみ</span><span class="sxs-lookup"><span data-stu-id="04210-145">How it works</span></span>

<span data-ttu-id="04210-146">リバース エンジニア リングは、データベース スキーマを読み取ることで開始します。</span><span class="sxs-lookup"><span data-stu-id="04210-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="04210-147">テーブル、列、制約、およびインデックスについての情報を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="04210-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="04210-148">次に、スキーマ情報を使用して EF Core モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="04210-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="04210-149">テーブルは、エンティティの種類の作成に使用されます。列は、プロパティの作成に使用されます。外部キー リレーションシップの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="04210-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="04210-150">最後に、モデルを使用して、コードを生成します。</span><span class="sxs-lookup"><span data-stu-id="04210-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="04210-151">アプリから同じモデルを再作成するためには、対応するエンティティ型のクラス、Fluent API、およびデータ注釈がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="04210-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="what-doesnt-work"></a><span data-ttu-id="04210-152">使用できないもの</span><span class="sxs-lookup"><span data-stu-id="04210-152">What doesn't work</span></span>

<span data-ttu-id="04210-153">データベース スキーマを使用してモデルに関するわけではない表現できます。</span><span class="sxs-lookup"><span data-stu-id="04210-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="04210-154">に関する情報など、**継承階層**、**所有型**、および**テーブル分割**がデータベース スキーマに存在しません。</span><span class="sxs-lookup"><span data-stu-id="04210-154">For example, information about **inheritance hierarchies**, **owned types**, and **table splitting** are not present in the database schema.</span></span> <span data-ttu-id="04210-155">このため、これらのコンストラクトは決してリバース エンジニア リングします。</span><span class="sxs-lookup"><span data-stu-id="04210-155">Because of this, these constructs will never be reverse engineered.</span></span>

<span data-ttu-id="04210-156">さらに、**列の型によって**EF Core プロバイダーでサポートされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="04210-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="04210-157">これらの列は、モデルに追加されません。</span><span class="sxs-lookup"><span data-stu-id="04210-157">These columns won't be included in the model.</span></span>

<span data-ttu-id="04210-158">EF Core では、すべてのエンティティ型キーにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="04210-158">EF Core requires every entity type to have a key.</span></span> <span data-ttu-id="04210-159">ただし、テーブル、プライマリ キーを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="04210-159">Tables, however, aren't required to specify a primary key.</span></span> <span data-ttu-id="04210-160">**主キーなしテーブル**はリバース エンジニア リングされた現在ありません。</span><span class="sxs-lookup"><span data-stu-id="04210-160">**Tables without a primary key** are currently not reverse engineered.</span></span>

<span data-ttu-id="04210-161">定義できます**同時実行制御トークン**を 2 人のユーザーが同時に同じエンティティを更新するを防ぐために、EF Core モデル。</span><span class="sxs-lookup"><span data-stu-id="04210-161">You can define **concurrency tokens** in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="04210-162">一部のデータベースがある場合は逆になっています。 この情報をエンジニア リングでこの種類の列 (たとえば、SQL Server での rowversion) を表す特殊な種類ただし、他の同時実行制御トークンがないリバース エンジニア リングします。</span><span class="sxs-lookup"><span data-stu-id="04210-162">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="04210-163">モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="04210-163">Customizing the model</span></span>

<span data-ttu-id="04210-164">EF Core によって生成されたコードは、コードです。</span><span class="sxs-lookup"><span data-stu-id="04210-164">The code generated by EF Core is your code.</span></span> <span data-ttu-id="04210-165">自由に変更できます。</span><span class="sxs-lookup"><span data-stu-id="04210-165">Feel free to change it.</span></span> <span data-ttu-id="04210-166">また場合、もう一度をリバース エンジニア リング、同じモデルのみ再生されます。</span><span class="sxs-lookup"><span data-stu-id="04210-166">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="04210-167">スキャフォールディングされたコードを表す*1 つ*には、データベースへのアクセスに使用できるモデルはありません、確かに、*のみ*のために使用できるモデル。</span><span class="sxs-lookup"><span data-stu-id="04210-167">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="04210-168">エンティティ型クラスと DbContext クラスは、ニーズに合わせてカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="04210-168">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="04210-169">たとえば、型とプロパティの名前を変更、継承の階層の導入または複数のエンティティにテーブルを分割することができます。</span><span class="sxs-lookup"><span data-stu-id="04210-169">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="04210-170">モデルから、一意でないインデックス、未使用のシーケンスとナビゲーション プロパティ、省略可能なスカラー プロパティ、および制約名を削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="04210-170">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="04210-171">追加のコンス トラクター、メソッド、プロパティなどを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="04210-171">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="04210-172">別のファイルに別の部分クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="04210-172">using another partial class in a separate file.</span></span> <span data-ttu-id="04210-173">このアプローチは、リバース エンジニア リング、モデルをもう一度する予定の場合にも動作します。</span><span class="sxs-lookup"><span data-stu-id="04210-173">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="04210-174">モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="04210-174">Updating the model</span></span>

<span data-ttu-id="04210-175">データベースに変更を行った後は、これらの変更を反映するように、EF Core モデルを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="04210-175">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="04210-176">データベースの変更が簡単な場合は、手動で、EF Core モデルに変更を加えるだけを最も簡単な場合があります。</span><span class="sxs-lookup"><span data-stu-id="04210-176">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="04210-177">たとえば、テーブルまたは列の名前を変更、列を削除または列の型を更新は、単純な変更をコードでは。</span><span class="sxs-lookup"><span data-stu-id="04210-177">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="04210-178">重要な変更は、ただし、いないことを簡単に手動でします。</span><span class="sxs-lookup"><span data-stu-id="04210-178">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="04210-179">エンジニア リングを使用してデータベースからモデルを 1 つの一般的なワークフローを逆に`-Force`(PMC) または`--force`1 つずつ更新で、既存のモデルを上書きする (CLI)。</span><span class="sxs-lookup"><span data-stu-id="04210-179">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="04210-180">もう 1 つの一般的に要求される機能は、名前変更、型の階層などのカスタマイズを維持しながら、データベースからモデルを更新する機能です。問題を使用して、 [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)この機能の進行状況を追跡します。</span><span class="sxs-lookup"><span data-stu-id="04210-180">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="04210-181">リバース エンジニア リングする、モデル、データベースからもう一度、ファイルに加えた変更はすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="04210-181">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
