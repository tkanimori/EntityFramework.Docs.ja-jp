---
title: 既存のデータベース - EF6 code First
author: divega
ms.date: 2016-10-23
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 29f959265e0fd0d5e14c156519e6931fd8da0677
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995091"
---
# <a name="code-first-to-an-existing-database"></a>既存のデータベースの code First
このビデオ、およびステップ バイ ステップ チュートリアルでは、既存のデータベースを対象とする Code First の開発の概要を提供します。 コードを最初に使用すると、C を使用して、モデル定義\#または VB.Net クラス。 必要に応じて追加の構成は、クラスやプロパティ、または fluent API を使用して属性を使用して実行できます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオは[Channel 9 で利用可能](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)します。

## <a name="pre-requisites"></a>前提条件

必要ですが**Visual Studio 2012**または**Visual Studio 2013**このチュートリアルを完了するをインストールします。

バージョンも必要になります**6.1** (またはそれ以降) の**Entity Framework Tools for Visual Studio**をインストールします。 参照してください[Entity Framework の取得](~/ef6/fundamentals/install.md)については、Entity Framework Tools の最新バージョンをインストールします。

## <a name="1-create-an-existing-database"></a>1.既存のデータベースを作成します。

通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。

データベースを生成してみましょう。

-   Visual Studio を開く
-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続 -&gt;接続の追加.**
-   データベースに接続していない場合は**サーバー エクスプ ローラー**を選択する必要があります前に**Microsoft SQL Server**データ ソースとして

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   LocalDB インスタンスに接続し、入力**ブログ**データベース名として

    ![LocalDBConnection](~/ef6/media/localdbconnection.png)

-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**
-   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2.アプリケーションを作成する

簡単に、Code First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のメニューと**コンソール アプリケーション**
-   入力**CodeFirstExistingDatabaseSample**名として
-   **[OK]** を選択します。

 

## <a name="3-reverse-engineer-model"></a>3.リバース エンジニア リング モデル

データベースにマップする初期コードを生成するために Entity Framework Tools for Visual Studio の使用になります。 これらのツールが入力することも手動で使用する場合のコードを生成するだけです。

-   **プロジェクト -&gt;新しい項目を追加しています.**
-   選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**
-   入力**BloggingContext**名をクリックします**OK**
-   これにより、起動、 **Entity Data Model ウィザード**
-   選択**データベースから Code First**クリック **[次へ]**

    ![WizardOneCFE](~/ef6/media/wizardonecfe.png)

-   最初のセクションで作成したデータベースへの接続を選択し、クリックして **[次へ]**

    ![WizardTwoCFE](~/ef6/media/wizardtwocfe.png)

-   横のチェック ボックスをクリックして**テーブル**すべてのテーブルのインポート をクリックして**完了**

    ![WizardThreeCFE](~/ef6/media/wizardthreecfe.png)

リバース エンジニア リング プロセスが完了項目の数が追加されて、プロジェクトにみましょう見て何が追加されています。

### <a name="configuration-file"></a>構成ファイル

App.config ファイルが追加されました、プロジェクトにこのファイルには、既存のデータベースへの接続文字列が含まれています。

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*わかります構成ファイル内の他の設定も、これらは Code First のデータベースを作成する場所を指示する既定の EF 設定です。アプリケーションでは、これらの設定は無視されます、既存のデータベースにマッピングしましたので。*

### <a name="derived-context"></a>派生コンテキスト

A **BloggingContext**クラスがプロジェクトに追加されました。 コンテキストは、クエリを実行し、データを保存することができます、データベースとのセッションを表します。
コンテキストを公開、 **DbSet&lt;TEntity&gt;** モデルの種類ごとにします。 また、基底コンス トラクターを使用して、既定のコンス トラクターを呼び出す、**名 =** 構文。 このコンテキストで使用する接続文字列を構成ファイルから読み込む必要がある Code First 指示します。

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*常に使用する必要があります、**名 =** 構文、構成ファイルで接続文字列を使用している場合。これにより、接続文字列が存在しない場合、Entity Framework がスローされます規則により、新しいデータベースを作成するのではなく。*

### <a name="model-classes"></a>モデル クラス

最後に、**ブログ**と**Post**クラスもプロジェクトに追加されました。 これらは、私たちのモデルを構成するドメイン クラスです。 Code First の規則は、既存のデータベースの構造と合っていない場所の構成を指定する、クラスに適用されるデータ注釈が表示されます。 たとえばに表示、 **StringLength**注釈**Blog.Name**と**Blog.Url**の最大長があるため**200**で、データベース (Code First の既定値がデータベース プロバイダー - でサポートされる最大長を使用するには**nvarchar (max)** SQL server)。

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4.読み取りとデータの書き込み

使用して一部のデータにアクセスするには、モデルができました。 実装、 **Main**メソッド**Program.cs**次のようです。 このコードは、コンテキストの新しいインスタンスを作成し、新しい挿入を使用して**ブログ**します。 すべてを取得する LINQ クエリを使用して**ブログ**でアルファベット順では、データベースから**タイトル**します。

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

アプリケーションを実行し、テストを実行できるようになりました。

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>データベースが変わった場合でしょうか。

Code First Database ウィザードには、クラスを調整し、変更することができますの開始地点のセットを生成しています。 データベース スキーマが変更された場合か、手動でクラスを編集したりクラスを上書きする別のリバース エンジニア リングを実行できます。

## <a name="using-code-first-migrations-to-an-existing-database"></a>既存のデータベースの Code First Migrations を使用します。

既存のデータベースで Code First Migrations を使用する場合を参照してください。[既存のデータベースの Code First Migrations](~/ef6/modeling/code-first/migrations/existing-database.md)します。

## <a name="summary"></a>まとめ

このチュートリアルでは、既存のデータベースを使用して Code First の開発に説明しました。 Entity Framework Tools for Visual Studio を使用して、リバース エンジニア リング、データベースにマップされ、データを格納および取得される可能性がありますクラスのセットをしました。
