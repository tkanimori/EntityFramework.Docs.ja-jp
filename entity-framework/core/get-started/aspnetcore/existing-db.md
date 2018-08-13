---
title: ASP.NET Core - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: c231a456abd4c110aba0326821799d6e9d567b3c
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614322"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a>既存のデータベースを使用した ASP.NET Core での EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。 既存のデータベースのリバース エンジニアリングによって Entity Framework モデルを作成します。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。

## <a name="prerequisites"></a>必須コンポーネント

以下のソフトウェアをインストールします。

* [Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) および以下のワークロード。
  * **ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)
  * **.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。

## <a name="create-blogging-database"></a>Blogging データベースを作成する

このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。 **Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略してください。

* Visual Studio を開く
* **[ツール] -> [データベースへの接続]**
* **[Microsoft SQL Server]** を選択して **[続行]** をクリックします。
* **[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。
* **[データベース名]** に「**master**」を入力して **[OK]** をクリックします。
* **[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。
* **[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。
* 以下に表示されているスクリプトを、クエリ エディターにコピーします。
* クエリ エディターを右クリックし、**[実行]** を選択します。

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* Visual Studio 2017 を開きます
* **[ファイル] > [新規] > [プロジェクト]**
* 左側のメニューから **[インストール済み] > [Visual C#] > [Web]** の順に選択します
* **[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します
* 名前に「**EFGetStarted.AspNetCore.ExistingDb**」を入力して **[OK]** をクリックします
* **[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます
* ターゲット フレームワークのドロップダウンが **[.NET Core]** に設定されており、バージョンのドロップダウンが **[ASP.NET Core 2.1]** に設定されていることを確認します
* **[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します
* **[認証]** に **[認証なし]** が設定されていることを確認します。
* **[OK]** をクリックします。

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。 

このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。 この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。

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

 エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。 こちらが、`Blog` と `Post` のエンティティ クラスです。

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> 遅延読み込みを有効にするため、ナビゲーション プロパティ `virtual` を作成することができます (Blog.Post および Post.Blog)。

 コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。

<!-- Static code listing, rather than a linked file, because the tutorial modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public BloggingContext()
    {
    }

    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    {
    }

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

### <a name="register-and-configure-your-context-in-startupcs"></a>Startup.cs にコンテキストを登録して構成する

`BloggingContext` を MVC コント ローラーで使用できるようにするには、サービスとして登録します。

* **Startup.cs** を開きます。
* ファイルの先頭に次の `using` ステートメントを追加します

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

`AddDbContext(...)` メソッドを使用して、サービスとして登録できるようになりました。
* `ConfigureServices(...)` メソッドを見つけます
* 強調表示されている次のコードを追加して、コンテキストをサービスとして登録します

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> 実際のアプリケーションでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。 わかりやすくするために、このチュートリアルではコード内に定義します。 詳細については、「[Connection Strings (接続文字列)](../../miscellaneous/connection-strings.md)」をご覧ください。

## <a name="create-a-controller-and-views"></a>コントローラーとビューの作成

* **ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] -> [コントローラー]** の順に選択します。
* **[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[OK]** をクリックします。
* **[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **[BloggingContext]** を設定します。
* **[追加]** をクリックします。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行して動作を確認できるようになりました。

* **[デバッグ] -> [デバッグなしで開始]**
* アプリケーションが作成され、Web ブラウザー内で開きます
* `/Blogs` に移動します。
* **[新規作成]** をクリックします。
* 新しいブログの **URL** を入力して、**[作成]** をクリックします。

![イメージ](_static/create.png)

![イメージ](_static/index-existing-db.png)
