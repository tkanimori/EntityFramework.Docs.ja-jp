---
title: リバースエンジニアリング-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414237"
---
# <a name="reverse-engineering"></a><span data-ttu-id="c1aaf-102">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="c1aaf-102">Reverse Engineering</span></span>

<span data-ttu-id="c1aaf-103">リバースエンジニアリングは、データベーススキーマに基づいてエンティティ型クラスおよび DbContext クラスをスキャフォールディングするプロセスです。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="c1aaf-104">これは、EF Core Package Manager Console (PMC) ツールの [`Scaffold-DbContext`] コマンド、または .NET コマンドラインインターフェイス (CLI) ツールの [`dotnet ef dbcontext scaffold`] コマンドを使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="c1aaf-105">インストール</span><span class="sxs-lookup"><span data-stu-id="c1aaf-105">Installing</span></span>

<span data-ttu-id="c1aaf-106">リバースエンジニアリングを行う前に、 [PMC ツール](xref:core/miscellaneous/cli/powershell)(Visual Studio のみ) または[CLI ツール](xref:core/miscellaneous/cli/dotnet)のいずれかをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="c1aaf-107">詳細については、「リンク」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-107">See links for details.</span></span>

<span data-ttu-id="c1aaf-108">また、リバースエンジニアリングするデータベーススキーマに適切な[データベースプロバイダー](xref:core/providers/index)をインストールする必要もあります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="c1aaf-109">接続文字列</span><span class="sxs-lookup"><span data-stu-id="c1aaf-109">Connection string</span></span>

<span data-ttu-id="c1aaf-110">コマンドの最初の引数は、データベースへの接続文字列です。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="c1aaf-111">ツールは、この接続文字列を使用してデータベーススキーマを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="c1aaf-112">接続文字列の引用符とエスケープ方法は、コマンドの実行に使用しているシェルによって異なります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="c1aaf-113">詳細については、シェルのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="c1aaf-114">たとえば、PowerShell では `$` 文字をエスケープする必要がありますが、`\`はできません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="c1aaf-115">構成とユーザーシークレット</span><span class="sxs-lookup"><span data-stu-id="c1aaf-115">Configuration and User Secrets</span></span>

<span data-ttu-id="c1aaf-116">ASP.NET Core のプロジェクトがある場合は、`Name=<connection-string>` 構文を使用して、構成から接続文字列を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="c1aaf-117">これは、[シークレットマネージャーツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)を使用すると、データベースのパスワードをコードベースとは別に保持するのに適しています。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="c1aaf-118">プロバイダー名</span><span class="sxs-lookup"><span data-stu-id="c1aaf-118">Provider name</span></span>

<span data-ttu-id="c1aaf-119">2番目の引数はプロバイダー名です。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-119">The second argument is the provider name.</span></span> <span data-ttu-id="c1aaf-120">プロバイダー名は、通常、プロバイダーの NuGet パッケージ名と同じです。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="c1aaf-121">テーブルの指定</span><span class="sxs-lookup"><span data-stu-id="c1aaf-121">Specifying tables</span></span>

<span data-ttu-id="c1aaf-122">データベーススキーマ内のすべてのテーブルは、既定でエンティティ型にリバースエンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="c1aaf-123">スキーマとテーブルを指定することにより、リバースエンジニアリングされるテーブルを制限できます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="c1aaf-124">PMC の `-Schemas` パラメーターと CLI の `--schema` オプションを使用して、スキーマ内のすべてのテーブルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="c1aaf-125">`-Tables` (PMC) および `--table` (CLI) を使用して、特定のテーブルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="c1aaf-126">PMC に複数のテーブルを含めるには、配列を使用します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="c1aaf-127">CLI に複数のテーブルを含めるには、オプションを複数回指定します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="c1aaf-128">名前の保持</span><span class="sxs-lookup"><span data-stu-id="c1aaf-128">Preserving names</span></span>

<span data-ttu-id="c1aaf-129">既定では、型およびプロパティの .NET の名前付け規則に一致するように、テーブル名と列名が固定されています。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="c1aaf-130">PMC のスイッチまたは CLI の `--use-database-names` オプション `-UseDatabaseNames` を指定すると、元のデータベース名を可能な限り保持したまま、この動作を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="c1aaf-131">無効な .NET 識別子は修正されますが、ナビゲーションプロパティのような名前は、.NET の名前付け規則に準拠したままになります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="c1aaf-132">Fluent API またはデータ注釈</span><span class="sxs-lookup"><span data-stu-id="c1aaf-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="c1aaf-133">エンティティ型は、既定では Fluent API を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="c1aaf-134">可能な場合は、`-DataAnnotations` (PMC) または `--data-annotations` (CLI) を指定して、代わりにデータ注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="c1aaf-135">たとえば、Fluent API を使用すると、次のようにスキャフォールディングます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="c1aaf-136">データ注釈を使用すると、次のようにスキャフォールディングます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="c1aaf-137">DbContext 名</span><span class="sxs-lookup"><span data-stu-id="c1aaf-137">DbContext name</span></span>

<span data-ttu-id="c1aaf-138">スキャフォールディング DbContext クラス名は、既定で*コンテキスト*がサフィックスとして付けられたデータベースの名前になります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="c1aaf-139">別のものを指定するには、PMC で `-Context` を使用し、CLI で `--context` します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="c1aaf-140">ディレクトリと名前空間</span><span class="sxs-lookup"><span data-stu-id="c1aaf-140">Directories and namespaces</span></span>

<span data-ttu-id="c1aaf-141">エンティティクラスと DbContext クラスは、プロジェクトのルートディレクトリにスキャフォールディングされ、プロジェクトの既定の名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="c1aaf-142">`-OutputDir` (PMC) または `--output-dir` (CLI) を使用して、クラスをスキャフォールディングするディレクトリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="c1aaf-143">名前空間は、ルート名前空間に、プロジェクトのルートディレクトリの下にあるサブディレクトリの名前を加えたものになります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="c1aaf-144">また、`-ContextDir` (PMC) および `--context-dir` (CLI) を使用して、エンティティ型クラスとは別のディレクトリに DbContext クラスをスキャフォールディングすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="c1aaf-145">しくみ</span><span class="sxs-lookup"><span data-stu-id="c1aaf-145">How it works</span></span>

<span data-ttu-id="c1aaf-146">リバースエンジニアリングは、データベーススキーマを読み取って開始します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="c1aaf-147">テーブル、列、制約、およびインデックスに関する情報を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="c1aaf-148">次に、スキーマ情報を使用して EF Core モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="c1aaf-149">テーブルは、エンティティ型を作成するために使用されます。列は、プロパティを作成するために使用されます。と外部キーを使用してリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="c1aaf-150">最後に、モデルを使用してコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="c1aaf-151">対応するエンティティ型クラス、Fluent API、およびデータ注釈は、アプリから同じモデルを再作成するためにスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="c1aaf-152">制限事項</span><span class="sxs-lookup"><span data-stu-id="c1aaf-152">Limitations</span></span>

* <span data-ttu-id="c1aaf-153">モデルに関するすべての情報は、データベーススキーマを使用して表すことはできません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="c1aaf-154">たとえば、[**継承階層**](../modeling/inheritance.md)、[**所有型**](../modeling/owned-entities.md)、および[**テーブル分割**](../modeling/table-splitting.md)に関する情報は、データベーススキーマには存在しません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-154">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="c1aaf-155">このため、これらの構造はリバースエンジニアリングされません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-155">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="c1aaf-156">また、**一部の列の型**は、EF Core プロバイダーでサポートされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="c1aaf-157">これらの列はモデルに含まれません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-157">These columns won't be included in the model.</span></span>
* <span data-ttu-id="c1aaf-158">EF Core モデルで[**同時実行トークン**](../modeling/concurrency.md)を定義して、2人のユーザーが同時に同じエンティティを更新できないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-158">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="c1aaf-159">一部のデータベースには、この種類の列を表す特殊な型 (SQL Server での rowversion など) があります。この場合、この情報をリバースエンジニアリングできます。ただし、その他の同時実行トークンはリバースエンジニアリングされません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-159">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="c1aaf-160">[現在C# 、次の8つの null 許容型機能](/dotnet/csharp/tutorials/nullable-reference-types)はリバースエンジニアリングではC#サポートされていません。 EF Core は、機能が無効であると想定しているコードを常に生成します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-160">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="c1aaf-161">たとえば、null 値が許容されるテキスト列は、プロパティが必要かどうかを構成するために使用される Fluent API またはデータ注釈が `string?`ではなく `string` 型のプロパティとしてスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-161">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="c1aaf-162">スキャフォールディングコードを編集し、null 値を許容C#する注釈に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-162">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="c1aaf-163">Null 許容型参照型のスキャフォールディングサポートは、 [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)問題によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-163">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="c1aaf-164">モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="c1aaf-164">Customizing the model</span></span>

<span data-ttu-id="c1aaf-165">EF Core によって生成されるコードはコードです。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-165">The code generated by EF Core is your code.</span></span> <span data-ttu-id="c1aaf-166">自由に変更できます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-166">Feel free to change it.</span></span> <span data-ttu-id="c1aaf-167">再生成されるのは、同じモデルをリバースエンジニアリングする場合だけです。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-167">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="c1aaf-168">スキャフォールディングコードは、データベースへのアクセスに使用できる*1 つ*のモデルを表しますが、使用できる*唯一*のモデルであるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-168">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="c1aaf-169">エンティティ型クラスと DbContext クラスをニーズに合わせてカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-169">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="c1aaf-170">たとえば、型とプロパティの名前を変更したり、継承階層を導入したり、テーブルを複数のエンティティに分割したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-170">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="c1aaf-171">また、一意でないインデックス、未使用のシーケンスとナビゲーションプロパティ、オプションのスカラープロパティ、および制約名をモデルから削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-171">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="c1aaf-172">コンストラクター、メソッド、プロパティなどを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-172">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="c1aaf-173">別のファイルで別の部分クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-173">using another partial class in a separate file.</span></span> <span data-ttu-id="c1aaf-174">この方法は、モデルをリバースエンジニアリングする場合でも機能します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-174">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="c1aaf-175">モデルを更新しています</span><span class="sxs-lookup"><span data-stu-id="c1aaf-175">Updating the model</span></span>

<span data-ttu-id="c1aaf-176">データベースに変更を加えた後、その変更を反映するために EF Core モデルを更新することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-176">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="c1aaf-177">データベースの変更が単純な場合は、EF Core モデルに手動で変更を加えるだけで簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-177">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="c1aaf-178">たとえば、テーブルまたは列の名前を変更したり、列を削除したり、列の型を更新したりすることは、コードを作成するための重要な変更です。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-178">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="c1aaf-179">ただし、大幅な変更は、手動で簡単に行うことができません。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-179">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="c1aaf-180">1つの一般的なワークフローは、`-Force` (PMC) または `--force` (CLI) を使用してデータベースからモデルを再度リバースエンジニアリングし、既存のモデルを更新されたモデルで上書きすることです。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-180">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="c1aaf-181">一般的に要求されるもう1つの機能は、名前変更、型階層などのカスタマイズを維持しながら、データベースからモデルを更新する機能です。この機能の進行状況を追跡するには、問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-181">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="c1aaf-182">データベースからモデルを再度リバースエンジニアリングする場合、ファイルに加えた変更はすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="c1aaf-182">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
