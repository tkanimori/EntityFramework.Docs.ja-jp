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
# <a name="reverse-engineering"></a>リバース エンジニア リング

リバース エンジニア リングは、エンティティ型クラスとデータベース スキーマに基づいて DbContext クラスをスキャフォールディングのプロセスです。 使用して、実行できる、 `Scaffold-DbContext` EF Core パッケージ マネージャー コンソール (PMC) ツールのコマンドまたは`dotnet ef dbcontext scaffold`.NET コマンド ライン インターフェイス (CLI) ツールのコマンド。

## <a name="installing"></a>インストール

リバース エンジニア リング、前に、いずれかをインストールする必要があります、 [PMC ツール](xref:core/miscellaneous/cli/powershell)(Visual Studio のみ) または[CLI ツール](xref:core/miscellaneous/cli/dotnet)します。 詳細については、リンクを参照してください。

適切なインストールも必要[データベース プロバイダー](xref:core/providers/index)データベース スキーマをリバース エンジニア リングします。

## <a name="connection-string"></a>接続文字列

コマンドの最初の引数は、データベースに接続文字列です。 ツールでは、この接続文字列を使用して、データベース スキーマを読み取る。

引用符で囲むし、接続文字列をエスケープする方法は、コマンドの実行に使用しているどのシェルに依存します。 詳細については、シェルのドキュメントを参照してください。 PowerShell をエスケープするために必要ななど、`$`しない場合は、文字`\`します。

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a>構成とユーザー シークレット

使用することができます、ASP.NET Core プロジェクトがあれば、`Name=<connection-string>`構成から接続文字列を確認するための構文。

これは連動、 [Secret Manager ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)コードベースからデータベースのパスワードを分離します。

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>プロバイダー名

2 番目の引数は、プロバイダーの名前です。 プロバイダー名は、通常プロバイダーの NuGet パッケージの名前と同じです。

## <a name="specifying-tables"></a>テーブルを指定します。

データベース スキーマのすべてのテーブルは、既定では、エンティティ型に組み込まれたリバースされます。 テーブルは、スキーマとテーブルを指定することでエンジニア リング リバースを制限することができます。

`-Schemas` PMC でパラメーターと`--schema`スキーマ内のすべてのテーブルを含めるには、CLI でのオプションを使用できます。

`-Tables` (PMC) と`--table`(CLI) を使用して特定のテーブルを含めることができます。

PMC で複数のテーブルを含めるには、配列を使用します。

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

CLI での複数のテーブルに含めるには、複数回オプションを指定します。

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a>名の保存

テーブルおよび列名は、既定で型とプロパティの .NET の名前付け規則に合わせて修正しました。 指定する、 `-UseDatabaseNames` PMC でスイッチまたは`--use-database-names`CLI でのオプションは、可能な限り、元のデータベース名を保持するこの動作を無効になります。 無効な .NET 識別子はまだ修正され、ナビゲーション プロパティと同様に合成された名前は、.NET の名前付け規則に準拠しています。

## <a name="fluent-api-or-data-annotations"></a>Fluent API や Data Annotations

エンティティ型、Fluent API を使用して既定で構成されます。 指定`-DataAnnotations`(PMC) または`--data-annotations`可能であれば、データ注釈を使用する (CLI)。

たとえば、Fluent API を使用してがスキャフォールディングこのします。

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

データ注釈を使用しているときにスキャフォールディングこれです。

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>DbContext の名前

スキャフォールディングの DbContext クラス名のサフィックスが付いたデータベースの名前になります*コンテキスト*既定。 使用して、別のアカウントを指定する`-Context`PMC でと`--context`CLI でします。

## <a name="directories-and-namespaces"></a>ディレクトリおよび名前空間

エンティティ クラスと DbContext クラスは、プロジェクトのルート ディレクトリにスキャフォールディングされ、プロジェクトの既定の名前空間を使用します。 ディレクトリを指定する場所を使用してクラスがスキャフォールディングされる`-OutputDir`(PMC) または`--output-dir`(CLI)。 名前空間は、ルート名前空間と、プロジェクトのルート ディレクトリの下の任意のサブディレクトリの名前になります。

使用することも`-ContextDir`(PMC) と`--context-dir`エンティティ型のクラスから別のディレクトリに、DbContext クラスをスキャフォールディングする (CLI)。

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a>しくみ

リバース エンジニア リングは、データベース スキーマを読み取ることで開始します。 テーブル、列、制約、およびインデックスについての情報を読み取ります。

次に、スキーマ情報を使用して EF Core モデルを作成します。 テーブルは、エンティティの種類の作成に使用されます。列は、プロパティの作成に使用されます。外部キー リレーションシップの作成に使用されます。

最後に、モデルを使用して、コードを生成します。 アプリから同じモデルを再作成するためには、対応するエンティティ型のクラス、Fluent API、およびデータ注釈がスキャフォールディングされます。

## <a name="what-doesnt-work"></a>使用できないもの

データベース スキーマを使用してモデルに関するわけではない表現できます。 に関する情報など、**継承階層**、**所有型**、および**テーブル分割**がデータベース スキーマに存在しません。 このため、これらのコンストラクトは決してリバース エンジニア リングします。

さらに、**列の型によって**EF Core プロバイダーでサポートされていない可能性があります。 これらの列は、モデルに追加されません。

EF Core では、すべてのエンティティ型キーにする必要があります。 ただし、テーブル、プライマリ キーを指定する必要はありません。 **主キーなしテーブル**はリバース エンジニア リングされた現在ありません。

定義できます**同時実行制御トークン**を 2 人のユーザーが同時に同じエンティティを更新するを防ぐために、EF Core モデル。 一部のデータベースがある場合は逆になっています。 この情報をエンジニア リングでこの種類の列 (たとえば、SQL Server での rowversion) を表す特殊な種類ただし、他の同時実行制御トークンがないリバース エンジニア リングします。

## <a name="customizing-the-model"></a>モデルのカスタマイズ

EF Core によって生成されたコードは、コードです。 自由に変更できます。 また場合、もう一度をリバース エンジニア リング、同じモデルのみ再生されます。 スキャフォールディングされたコードを表す*1 つ*には、データベースへのアクセスに使用できるモデルはありません、確かに、*のみ*のために使用できるモデル。

エンティティ型クラスと DbContext クラスは、ニーズに合わせてカスタマイズします。 たとえば、型とプロパティの名前を変更、継承の階層の導入または複数のエンティティにテーブルを分割することができます。 モデルから、一意でないインデックス、未使用のシーケンスとナビゲーション プロパティ、省略可能なスカラー プロパティ、および制約名を削除することもできます。

追加のコンス トラクター、メソッド、プロパティなどを追加することもできます。 別のファイルに別の部分クラスを使用します。 このアプローチは、リバース エンジニア リング、モデルをもう一度する予定の場合にも動作します。

## <a name="updating-the-model"></a>モデルを更新します。

データベースに変更を行った後は、これらの変更を反映するように、EF Core モデルを更新する必要があります。 データベースの変更が簡単な場合は、手動で、EF Core モデルに変更を加えるだけを最も簡単な場合があります。 たとえば、テーブルまたは列の名前を変更、列を削除または列の型を更新は、単純な変更をコードでは。

重要な変更は、ただし、いないことを簡単に手動でします。 エンジニア リングを使用してデータベースからモデルを 1 つの一般的なワークフローを逆に`-Force`(PMC) または`--force`1 つずつ更新で、既存のモデルを上書きする (CLI)。

もう 1 つの一般的に要求される機能は、名前変更、型の階層などのカスタマイズを維持しながら、データベースからモデルを更新する機能です。問題を使用して、 [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)この機能の進行状況を追跡します。

> [!WARNING]
> リバース エンジニア リングする、モデル、データベースからもう一度、ファイルに加えた変更はすべて失われます。
