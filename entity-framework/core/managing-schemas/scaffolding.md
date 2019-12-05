---
title: リバースエンジニアリング-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824468"
---
# <a name="reverse-engineering"></a>リバースエンジニアリング

リバースエンジニアリングは、データベーススキーマに基づいてエンティティ型クラスおよび DbContext クラスをスキャフォールディングするプロセスです。 これは、EF Core Package Manager Console (PMC) ツールの [`Scaffold-DbContext`] コマンド、または .NET コマンドラインインターフェイス (CLI) ツールの [`dotnet ef dbcontext scaffold`] コマンドを使用して実行できます。

## <a name="installing"></a>のインストール

リバースエンジニアリングを行う前に、 [PMC ツール](xref:core/miscellaneous/cli/powershell)(Visual Studio のみ) または[CLI ツール](xref:core/miscellaneous/cli/dotnet)のいずれかをインストールする必要があります。 詳細については、「リンク」を参照してください。

また、リバースエンジニアリングするデータベーススキーマに適切な[データベースプロバイダー](xref:core/providers/index)をインストールする必要もあります。

## <a name="connection-string"></a>接続文字列

コマンドの最初の引数は、データベースへの接続文字列です。 ツールは、この接続文字列を使用してデータベーススキーマを読み取ります。

接続文字列の引用符とエスケープ方法は、コマンドの実行に使用しているシェルによって異なります。 詳細については、シェルのドキュメントを参照してください。 たとえば、PowerShell では `$` 文字をエスケープする必要がありますが、`\`はできません。

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a>構成とユーザーシークレット

ASP.NET Core のプロジェクトがある場合は、`Name=<connection-string>` 構文を使用して、構成から接続文字列を読み取ることができます。

これは、[シークレットマネージャーツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)を使用すると、データベースのパスワードをコードベースとは別に保持するのに適しています。

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>プロバイダー名

2番目の引数はプロバイダー名です。 プロバイダー名は、通常、プロバイダーの NuGet パッケージ名と同じです。

## <a name="specifying-tables"></a>テーブルの指定

データベーススキーマ内のすべてのテーブルは、既定でエンティティ型にリバースエンジニアリングされます。 スキーマとテーブルを指定することにより、リバースエンジニアリングされるテーブルを制限できます。

PMC の `-Schemas` パラメーターと CLI の `--schema` オプションを使用して、スキーマ内のすべてのテーブルを含めることができます。

`-Tables` (PMC) および `--table` (CLI) を使用して、特定のテーブルを含めることができます。

PMC に複数のテーブルを含めるには、配列を使用します。

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

CLI に複数のテーブルを含めるには、オプションを複数回指定します。

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a>名前の保持

既定では、型およびプロパティの .NET の名前付け規則に一致するように、テーブル名と列名が固定されています。 PMC のスイッチまたは CLI の `--use-database-names` オプション `-UseDatabaseNames` を指定すると、元のデータベース名を可能な限り保持したまま、この動作を無効にすることができます。 無効な .NET 識別子は修正されますが、ナビゲーションプロパティのような名前は、.NET の名前付け規則に準拠したままになります。

## <a name="fluent-api-or-data-annotations"></a>Fluent API またはデータ注釈

エンティティ型は、既定では Fluent API を使用して構成されます。 可能な場合は、`-DataAnnotations` (PMC) または `--data-annotations` (CLI) を指定して、代わりにデータ注釈を使用します。

たとえば、Fluent API を使用すると、次のようにスキャフォールディングます。

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

データ注釈を使用すると、次のようにスキャフォールディングます。

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>DbContext 名

スキャフォールディング DbContext クラス名は、既定で*コンテキスト*がサフィックスとして付けられたデータベースの名前になります。 別のものを指定するには、PMC で `-Context` を使用し、CLI で `--context` します。

## <a name="directories-and-namespaces"></a>ディレクトリと名前空間

エンティティクラスと DbContext クラスは、プロジェクトのルートディレクトリにスキャフォールディングされ、プロジェクトの既定の名前空間を使用します。 `-OutputDir` (PMC) または `--output-dir` (CLI) を使用して、クラスをスキャフォールディングするディレクトリを指定できます。 名前空間は、ルート名前空間に、プロジェクトのルートディレクトリの下にあるサブディレクトリの名前を加えたものになります。

また、`-ContextDir` (PMC) および `--context-dir` (CLI) を使用して、エンティティ型クラスとは別のディレクトリに DbContext クラスをスキャフォールディングすることもできます。

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a>機能のしくみ

リバースエンジニアリングは、データベーススキーマを読み取って開始します。 テーブル、列、制約、およびインデックスに関する情報を読み取ります。

次に、スキーマ情報を使用して EF Core モデルを作成します。 テーブルは、エンティティ型を作成するために使用されます。列は、プロパティを作成するために使用されます。と外部キーを使用してリレーションシップを作成します。

最後に、モデルを使用してコードを生成します。 対応するエンティティ型クラス、Fluent API、およびデータ注釈は、アプリから同じモデルを再作成するためにスキャフォールディングされます。

## <a name="limitations"></a>制限事項

* モデルに関するすべての情報は、データベーススキーマを使用して表すことはできません。 たとえば、[**継承階層**](../modeling/inheritance.md)、[**所有型**](../modeling/owned-entities.md)、および[**テーブル分割**](../modeling/table-splitting.md)に関する情報は、データベーススキーマには存在しません。 このため、これらの構造はリバースエンジニアリングされません。
* また、**一部の列の型**は、EF Core プロバイダーでサポートされない場合があります。 これらの列はモデルに含まれません。
* EF Core モデルで[**同時実行トークン**](../modeling/concurrency.md)を定義して、2人のユーザーが同時に同じエンティティを更新できないようにすることができます。 一部のデータベースには、この種類の列を表す特殊な型 (SQL Server での rowversion など) があります。この場合、この情報をリバースエンジニアリングできます。ただし、その他の同時実行トークンはリバースエンジニアリングされません。
* [現在C# 、次の8つの null 許容型機能](/dotnet/csharp/tutorials/nullable-reference-types)はリバースエンジニアリングではC#サポートされていません。 EF Core は、機能が無効であると想定しているコードを常に生成します。 たとえば、null 値が許容されるテキスト列は、プロパティが必要かどうかを構成するために使用される Fluent API またはデータ注釈が `string?`ではなく `string` 型のプロパティとしてスキャフォールディングされます。 スキャフォールディングコードを編集し、null 値を許容C#する注釈に置き換えることができます。 Null 許容型参照型のスキャフォールディングサポートは、 [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)問題によって追跡されます。

## <a name="customizing-the-model"></a>モデルのカスタマイズ

EF Core によって生成されるコードはコードです。 自由に変更できます。 再生成されるのは、同じモデルをリバースエンジニアリングする場合だけです。 スキャフォールディングコードは、データベースへのアクセスに使用できる*1 つ*のモデルを表しますが、使用できる*唯一*のモデルであるとは限りません。

エンティティ型クラスと DbContext クラスをニーズに合わせてカスタマイズします。 たとえば、型とプロパティの名前を変更したり、継承階層を導入したり、テーブルを複数のエンティティに分割したりすることができます。 また、一意でないインデックス、未使用のシーケンスとナビゲーションプロパティ、オプションのスカラープロパティ、および制約名をモデルから削除することもできます。

コンストラクター、メソッド、プロパティなどを追加することもできます。 別のファイルで別の部分クラスを使用しています。 この方法は、モデルをリバースエンジニアリングする場合でも機能します。

## <a name="updating-the-model"></a>モデルを更新しています

データベースに変更を加えた後、その変更を反映するために EF Core モデルを更新することが必要になる場合があります。 データベースの変更が単純な場合は、EF Core モデルに手動で変更を加えるだけで簡単に行うことができます。 たとえば、テーブルまたは列の名前を変更したり、列を削除したり、列の型を更新したりすることは、コードを作成するための重要な変更です。

ただし、大幅な変更は、手動で簡単に行うことができません。 1つの一般的なワークフローは、`-Force` (PMC) または `--force` (CLI) を使用してデータベースからモデルを再度リバースエンジニアリングし、既存のモデルを更新されたモデルで上書きすることです。

一般的に要求されるもう1つの機能は、名前変更、型階層などのカスタマイズを維持しながら、データベースからモデルを更新する機能です。この機能の進行状況を追跡するには、問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)を使用します。

> [!WARNING]
> データベースからモデルを再度リバースエンジニアリングする場合、ファイルに加えた変更はすべて失われます。
