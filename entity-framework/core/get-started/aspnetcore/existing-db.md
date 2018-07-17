---
title: ASP.NET Core - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: e28149346ccd7531449ea696505588317471e6dd
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949154"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a>既存のデータベースを使用した ASP.NET Core での EF Core の概要

このチュートリアルでは、Entity Framework を使用して基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。 既存のデータベースに基づく Entity Framework モデルを作成するために、リバース エンジニアリングを使用します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)は GitHub で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するには、次の条件を満たす必要があります。

* [Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) および以下のワークロード。
  * **ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)
  * **.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。
* [Blogging データベース](#blogging-database)

### <a name="blogging-database"></a>Blogging データベース

このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。

> [!TIP]  
> **Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略できます。

* Visual Studio を開く
* **[ツール] -> [データベースへの接続]**
* **[Microsoft SQL Server]** を選択して **[続行]** をクリックします。
* **[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。
* **[データベース名]** に「**master**」を入力して **[OK]** をクリックします。
* **[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。
* **[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。
* 以下に表示されているスクリプトを、クエリ エディターにコピーします
* クエリ エディターを右クリックし、**[実行]** を選択します。

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio 2017 を開きます
* **[ファイル] -> [新規] -> [プロジェクト...]**
* 左側のメニューから **[インストール済み] -> [テンプレート] -> [Visual C#] -> [Web]** の順に選択します。
* **[ASP.NET Core Web アプリケーション (.NET Core)]** プロジェクト テンプレートを選択します。
* 名前に「**EFGetStarted.AspNetCore.ExistingDb**」を入力して **[OK]** をクリックします
* **[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます
* **[ASP.NET Core Templates 2.0]\(ASP.NET Core テンプレート 2.0\)** で、**[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。
* **[認証]** に **[認証なし]** が設定されていることを確認します。
* **[OK]** をクリックします。

## <a name="install-entity-framework"></a>Entity Framework をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQL Server を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**

* `Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。

いくつかの Entity Framework Tools を使用して、データベースからモデルを作成します。 そのため、ツールのパッケージをインストールします。

* `Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。

後で、いくつかの ASP.NET Core スキャフォールディング ツールを使用して、コント ローラーとビューを作成します。 そのため、このデザイン パッケージもインストールします。

* `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design` を実行します。

## <a name="reverse-engineer-your-model"></a>モデルのリバース エンジニアリングを行う

ここで、お使いの既存のデータベースに基づいて、EF モデルを作成します。

* **[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**
* 次のコマンドを実行して、既存のデータベースからモデルを作成します。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

`The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。

> [!TIP]  
> 上記のコマンドに引数 `-Tables` を追加すると、エンティティを生成するテーブルを指定できます。 たとえば、`-Tables Blog,Post` のようにします。

リバース エンジニアリングのプロセスにより、既存のデータベースのスキーマに基づいてエンティティ クラス (`Blog.cs` & `Post.cs`) と派生コンテキスト (`BloggingContext.cs`) が作成されます。

 エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

 コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。

<!-- Static code listing, rather than a linked file, because the walkthrough modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public virtual DbSet<Blog> Blog { get; set; }
    public virtual DbSet<Post> Post { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>(entity =>
        {
            entity.Property(e => e.Url).IsRequired();
        });

        modelBuilder.Entity<Post>(entity =>
        {
            entity.HasOne(d => d.Blog)
                .WithMany(p => p.Post)
                .HasForeignKey(d => d.BlogId);
        });
    }
}
```

## <a name="register-your-context-with-dependency-injection"></a>依存関係の挿入にコンテキストを登録します。

依存関係の挿入の概念は、ASP.NET Core の中心となるものです。 サービス (`BloggingContext` など) は、アプリケーションの起動時に依存関係の挿入に登録されます。 これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。 依存関係の挿入の詳細については、ASP.NET サイトの[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に関する記事をご覧ください。

### <a name="remove-inline-context-configuration"></a>インライン コンテキスト構成の削除

ASP.NET Core では、**Startup.cs** 内で構成を行うのが一般的です。 このパターンに合わせるために、データベース プロバイダーの構成を **Startup.cs** に移動します。

* `Models\BloggingContext.cs` を開きます
* `OnConfiguring(...)` メソッドを削除します

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
    optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
}
```

* 次のコンストラクターを追加すると、依存関係の挿入によって構成をコンテキストに渡すことができます。

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/BloggingContext.cs#Constructor)]

### <a name="register-and-configure-your-context-in-startupcs"></a>Startup.cs にコンテキストを登録して構成する

MVC コントローラーで利用するために、`BloggingContext` をサービスとして登録します。

* **Startup.cs** を開きます。
* ファイルの先頭に次の `using` ステートメントを追加します

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

`AddDbContext(...)` メソッドを使用して、BloggingContext をサービスとして登録できるようになりました。
* `ConfigureServices(...)` メソッドを見つけます
* 次のコードを追加して、コンテキストをサービスとして登録します

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

> [!TIP]  
> 実際のアプリケーションでは、接続文字列は構成ファイルに記述するのが一般的です。 わかりやすくするために、接続文字列はコード内に定義しています。 詳細については、「[Connection Strings (接続文字列)](../../miscellaneous/connection-strings.md)」をご覧ください。

## <a name="create-a-controller"></a>コントローラーを作成する

次に、プロジェクトでスキャフォールディングを有効にします。

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] -> [コントローラー]** の順に選択します。
* **[完全な依存関係]** を選択して **[追加]** をクリックします。
* 開いた `ScaffoldingReadMe.txt` ファイル内の指示は無視してかまいません。

スキャフォールディングが有効になったら、`Blog` エンティティのコントローラーをスキャフォールディングできます。

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] -> [コントローラー]** の順に選択します。
* **[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[OK]** をクリックします。
* **[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **[BloggingContext]** を設定します。
* **[追加]** をクリックします。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行して動作を確認できるようになりました。

* **[デバッグ] -> [デバッグなしで開始]**
* アプリケーションが作成され、Web ブラウザー上で開かれます。
* `/Blogs` に移動します。
* **[新規作成]** をクリックします。
* 新しいブログの **URL** を入力して、**[作成]** をクリックします。

![イメージ](_static/create.png)

![イメージ](_static/index-existing-db.png)
