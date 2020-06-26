---
title: リバースエンジニアリング-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 2422e7455dc4ef52f0a9d3bdaebfa02f62e6d50f
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370599"
---
# <a name="reverse-engineering"></a><span data-ttu-id="be2a1-102"> リバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="be2a1-102">Reverse Engineering</span></span>

<span data-ttu-id="be2a1-103">リバースエンジニアリングは、データベーススキーマに基づいてエンティティ型クラスおよび DbContext クラスをスキャフォールディングするプロセスです。</span><span class="sxs-lookup"><span data-stu-id="be2a1-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="be2a1-104">これは、 `Scaffold-DbContext` EF Core パッケージマネージャーコンソール (PMC) ツールのコマンド、または `dotnet ef dbcontext scaffold` .net コマンドラインインターフェイス (CLI) ツールのコマンドを使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="be2a1-105">インストール</span><span class="sxs-lookup"><span data-stu-id="be2a1-105">Installing</span></span>

<span data-ttu-id="be2a1-106">リバースエンジニアリングを行う前に、 [PMC ツール](xref:core/miscellaneous/cli/powershell)(Visual Studio のみ) または[CLI ツール](xref:core/miscellaneous/cli/dotnet)のいずれかをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="be2a1-107">詳細については、「リンク」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="be2a1-107">See links for details.</span></span>

<span data-ttu-id="be2a1-108">また、リバースエンジニアリングするデータベーススキーマに適切な[データベースプロバイダー](xref:core/providers/index)をインストールする必要もあります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="be2a1-109">接続文字列</span><span class="sxs-lookup"><span data-stu-id="be2a1-109">Connection string</span></span>

<span data-ttu-id="be2a1-110">コマンドの最初の引数は、データベースへの接続文字列です。</span><span class="sxs-lookup"><span data-stu-id="be2a1-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="be2a1-111">ツールは、この接続文字列を使用してデータベーススキーマを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="be2a1-112">接続文字列の引用符とエスケープ方法は、コマンドの実行に使用しているシェルによって異なります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="be2a1-113">詳細については、シェルのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="be2a1-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="be2a1-114">たとえば、PowerShell では文字をエスケープする必要がありますが、は使用 `$` しません `\` 。</span><span class="sxs-lookup"><span data-stu-id="be2a1-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="be2a1-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="be2a1-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="be2a1-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be2a1-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="be2a1-117">構成とユーザーシークレット</span><span class="sxs-lookup"><span data-stu-id="be2a1-117">Configuration and User Secrets</span></span>

<span data-ttu-id="be2a1-118">ASP.NET Core のプロジェクトがある場合は、構文を使用して `Name=<connection-string>` 構成から接続文字列を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-118">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="be2a1-119">これは、[シークレットマネージャーツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)を使用すると、データベースのパスワードをコードベースとは別に保持するのに適しています。</span><span class="sxs-lookup"><span data-stu-id="be2a1-119">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="be2a1-120">プロバイダー名</span><span class="sxs-lookup"><span data-stu-id="be2a1-120">Provider name</span></span>

<span data-ttu-id="be2a1-121">2番目の引数はプロバイダー名です。</span><span class="sxs-lookup"><span data-stu-id="be2a1-121">The second argument is the provider name.</span></span> <span data-ttu-id="be2a1-122">プロバイダー名は、通常、プロバイダーの NuGet パッケージ名と同じです。</span><span class="sxs-lookup"><span data-stu-id="be2a1-122">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="be2a1-123">テーブルの指定</span><span class="sxs-lookup"><span data-stu-id="be2a1-123">Specifying tables</span></span>

<span data-ttu-id="be2a1-124">データベーススキーマ内のすべてのテーブルは、既定でエンティティ型にリバースエンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-124">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="be2a1-125">スキーマとテーブルを指定することにより、リバースエンジニアリングされるテーブルを制限できます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-125">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="be2a1-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="be2a1-126">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="be2a1-127">オプションを使用すると、スキーマ内のすべてのテーブルを含めることができますが、を使用して `--schema` `--table` 特定のテーブルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-127">The `--schema` option can be used to include every table within a schema, while `--table` can be used to include specific tables.</span></span>

<span data-ttu-id="be2a1-128">複数のテーブルを含めるには、オプションを複数回指定します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-128">To include multiple tables, specify the option multiple times:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[<span data-ttu-id="be2a1-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be2a1-129">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="be2a1-130">オプションを使用すると、スキーマ内のすべてのテーブルを含めることができますが、を使用して `-Schemas` `-Tables` 特定のテーブルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-130">The `-Schemas` option can be used to include every table within a schema, while `-Tables` can be used to include specific tables.</span></span>

<span data-ttu-id="be2a1-131">複数のテーブルを含めるには、次のように配列を使用します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-131">To include multiple tables, use an array:</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a><span data-ttu-id="be2a1-132">名前の保持</span><span class="sxs-lookup"><span data-stu-id="be2a1-132">Preserving names</span></span>

<span data-ttu-id="be2a1-133">既定では、型およびプロパティの .NET の名前付け規則に一致するように、テーブル名と列名が固定されています。</span><span class="sxs-lookup"><span data-stu-id="be2a1-133">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="be2a1-134">PMC でスイッチを指定するか .NET Core CLI でオプションを指定すると、 `-UseDatabaseNames` `--use-database-names` 元のデータベース名を可能な限り保持したまま、この動作が無効になります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-134">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the .NET Core CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="be2a1-135">無効な .NET 識別子は修正されますが、ナビゲーションプロパティのような名前は、.NET の名前付け規則に準拠したままになります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-135">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="be2a1-136">Fluent API またはデータ注釈</span><span class="sxs-lookup"><span data-stu-id="be2a1-136">Fluent API or Data Annotations</span></span>

<span data-ttu-id="be2a1-137">エンティティ型は、既定では Fluent API を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-137">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="be2a1-138">`-DataAnnotations`可能な場合は、(PMC) または `--data-annotations` (.NET Core CLI) を指定して、代わりにデータ注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-138">Specify `-DataAnnotations` (PMC) or `--data-annotations` (.NET Core CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="be2a1-139">たとえば、Fluent API を使用すると、次のようにスキャフォールディングます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-139">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="be2a1-140">データ注釈を使用すると、次のようにスキャフォールディングます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-140">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="be2a1-141">DbContext 名</span><span class="sxs-lookup"><span data-stu-id="be2a1-141">DbContext name</span></span>

<span data-ttu-id="be2a1-142">スキャフォールディング DbContext クラス名は、既定で*コンテキスト*がサフィックスとして付けられたデータベースの名前になります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-142">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="be2a1-143">別のものを指定するには、PMC でを使用し、.NET Core CLI でを使用し `-Context` `--context` ます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-143">To specify a different one, use `-Context` in PMC and `--context` in the .NET Core CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="be2a1-144">ディレクトリと名前空間</span><span class="sxs-lookup"><span data-stu-id="be2a1-144">Directories and namespaces</span></span>

<span data-ttu-id="be2a1-145">エンティティクラスと DbContext クラスは、プロジェクトのルートディレクトリにスキャフォールディングされ、プロジェクトの既定の名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-145">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="be2a1-146">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="be2a1-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="be2a1-147">クラスがスキャフォールディングを使用しているディレクトリを指定することができます。また、このディレクトリを使用して、 `--output-dir` `--context-dir` エンティティ型クラスとは別のディレクトリに dbcontext クラスをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-147">You can specify the directory where classes are scaffolded using `--output-dir`, and `--context-dir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

<span data-ttu-id="be2a1-148">既定では、名前空間は、ルート名前空間に、プロジェクトのルートディレクトリの下にあるサブディレクトリの名前を加えたものになります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-148">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="be2a1-149">ただし、EFCore 5.0 以降では、を使用して、すべての出力クラスの名前空間をオーバーライドでき `--namespace` ます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-149">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `--namespace`.</span></span> <span data-ttu-id="be2a1-150">次のものを使用して、DbContext クラスだけの名前空間をオーバーライドすることもでき `--context-namespace` ます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-150">You can also override the namespace for just the DbContext class using `--context-namespace`:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="be2a1-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be2a1-151">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="be2a1-152">クラスがスキャフォールディングを使用しているディレクトリを指定することができます。また、このディレクトリを使用して、 `-OutputDir` `-ContextDir` エンティティ型クラスとは別のディレクトリに dbcontext クラスをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-152">You can specify the directory where classes are scaffolded using `-OutputDir`, and `-ContextDir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

<span data-ttu-id="be2a1-153">既定では、名前空間は、ルート名前空間に、プロジェクトのルートディレクトリの下にあるサブディレクトリの名前を加えたものになります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-153">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="be2a1-154">ただし、EFCore 5.0 以降では、を使用して、すべての出力クラスの名前空間をオーバーライドでき `-Namespace` ます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-154">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `-Namespace`.</span></span> <span data-ttu-id="be2a1-155">また、を使用して、DbContext クラスの名前空間だけをオーバーライドすることもでき `-ContextNamespace` ます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-155">You can also override the namespace for just the DbContext class using `-ContextNamespace`.</span></span>

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a><span data-ttu-id="be2a1-156">しくみ</span><span class="sxs-lookup"><span data-stu-id="be2a1-156">How it works</span></span>

<span data-ttu-id="be2a1-157">リバースエンジニアリングは、データベーススキーマを読み取って開始します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-157">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="be2a1-158">テーブル、列、制約、およびインデックスに関する情報を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-158">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="be2a1-159">次に、スキーマ情報を使用して EF Core モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-159">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="be2a1-160">テーブルは、エンティティ型を作成するために使用されます。列は、プロパティを作成するために使用されます。と外部キーを使用してリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-160">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="be2a1-161">最後に、モデルを使用してコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-161">Finally, the model is used to generate code.</span></span> <span data-ttu-id="be2a1-162">対応するエンティティ型クラス、Fluent API、およびデータ注釈は、アプリから同じモデルを再作成するためにスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-162">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="be2a1-163">制限事項</span><span class="sxs-lookup"><span data-stu-id="be2a1-163">Limitations</span></span>

* <span data-ttu-id="be2a1-164">モデルに関するすべての情報は、データベーススキーマを使用して表すことはできません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-164">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="be2a1-165">たとえば、[**継承階層**](../modeling/inheritance.md)、[**所有型**](../modeling/owned-entities.md)、および[**テーブル分割**](../modeling/table-splitting.md)に関する情報は、データベーススキーマには存在しません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-165">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="be2a1-166">このため、これらの構造はリバースエンジニアリングされません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-166">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="be2a1-167">また、**一部の列の型**は、EF Core プロバイダーでサポートされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-167">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="be2a1-168">これらの列はモデルに含まれません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-168">These columns won't be included in the model.</span></span>
* <span data-ttu-id="be2a1-169">EF Core モデルで[**同時実行トークン**](../modeling/concurrency.md)を定義して、2人のユーザーが同時に同じエンティティを更新できないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-169">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="be2a1-170">一部のデータベースには、この種類の列を表す特殊な型 (SQL Server での rowversion など) があります。この場合、この情報をリバースエンジニアリングできます。ただし、その他の同時実行トークンはリバースエンジニアリングされません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-170">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="be2a1-171">[C# 8 nullable 参照型機能は、現在、](/dotnet/csharp/tutorials/nullable-reference-types)リバースエンジニアリングではサポートされていません。 EF Core は、機能が無効になっていると想定している c# コードを常に生成します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-171">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="be2a1-172">たとえば、null 値が許容されるテキスト列は、 `string` `string?` プロパティが必須かどうかを構成するために使用される Fluent API またはデータ注釈を使用してではなく、型のプロパティとしてスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-172">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="be2a1-173">スキャフォールディングコードを編集し、C# の Null 値の許容属性に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-173">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="be2a1-174">Null 許容型参照型のスキャフォールディングサポートは、 [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)問題によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-174">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="be2a1-175">モデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="be2a1-175">Customizing the model</span></span>

<span data-ttu-id="be2a1-176">EF Core によって生成されるコードはコードです。</span><span class="sxs-lookup"><span data-stu-id="be2a1-176">The code generated by EF Core is your code.</span></span> <span data-ttu-id="be2a1-177">自由に変更できます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-177">Feel free to change it.</span></span> <span data-ttu-id="be2a1-178">再生成されるのは、同じモデルをリバースエンジニアリングする場合だけです。</span><span class="sxs-lookup"><span data-stu-id="be2a1-178">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="be2a1-179">スキャフォールディングコードは、データベースへのアクセスに使用できる*1 つ*のモデルを表しますが、使用できる*唯一*のモデルであるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-179">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="be2a1-180">エンティティ型クラスと DbContext クラスをニーズに合わせてカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="be2a1-180">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="be2a1-181">たとえば、型とプロパティの名前を変更したり、継承階層を導入したり、テーブルを複数のエンティティに分割したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-181">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="be2a1-182">また、一意でないインデックス、未使用のシーケンスとナビゲーションプロパティ、オプションのスカラープロパティ、および制約名をモデルから削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-182">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="be2a1-183">コンストラクター、メソッド、プロパティなどを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-183">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="be2a1-184">別のファイルで別の部分クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="be2a1-184">using another partial class in a separate file.</span></span> <span data-ttu-id="be2a1-185">この方法は、モデルをリバースエンジニアリングする場合でも機能します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-185">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="be2a1-186">モデルを更新しています</span><span class="sxs-lookup"><span data-stu-id="be2a1-186">Updating the model</span></span>

<span data-ttu-id="be2a1-187">データベースに変更を加えた後、その変更を反映するために EF Core モデルを更新することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="be2a1-187">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="be2a1-188">データベースの変更が単純な場合は、EF Core モデルに手動で変更を加えるだけで簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-188">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="be2a1-189">たとえば、テーブルまたは列の名前を変更したり、列を削除したり、列の型を更新したりすることは、コードを作成するための重要な変更です。</span><span class="sxs-lookup"><span data-stu-id="be2a1-189">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="be2a1-190">ただし、大幅な変更は、手動で簡単に行うことができません。</span><span class="sxs-lookup"><span data-stu-id="be2a1-190">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="be2a1-191">一般的なワークフローの1つは、 `-Force` (PMC) または (CLI) を使用してデータベースからモデルを再度リバースエンジニアリングし、 `--force` 既存のモデルを更新されたモデルで上書きすることです。</span><span class="sxs-lookup"><span data-stu-id="be2a1-191">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="be2a1-192">一般的に要求されるもう1つの機能は、名前変更、型階層などのカスタマイズを維持しながら、データベースからモデルを更新する機能です。この機能の進行状況を追跡するには、[問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)を使用します。</span><span class="sxs-lookup"><span data-stu-id="be2a1-192">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="be2a1-193">データベースからモデルを再度リバースエンジニアリングする場合、ファイルに加えた変更はすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="be2a1-193">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
