---
title: 既存のデータベースへの Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 0a51f826422d7e2bff33b968605eace1e754c425
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415599"
---
# <a name="code-first-to-an-existing-database"></a>既存のデータベースへの Code First
このビデオとステップバイステップのチュートリアルでは、既存のデータベースを対象とした Code First 開発の概要について説明します。 Code First では、C\# または VB.Net クラスを使用してモデルを定義できます。 必要に応じて、クラスとプロパティの属性、または fluent API を使用して、追加の構成を行うことができます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオは、 [Channel 9 で利用できるようになりました](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、 **Visual Studio 2012**または**Visual Studio 2013**がインストールされている必要があります。

また、 **Visual Studio の Entity Framework Tools**バージョン**6.1** (またはそれ以降) がインストールされている必要もあります。 最新バージョンの Entity Framework Tools をインストールする方法については、「 [Get Entity Framework](~/ef6/fundamentals/install.md) 」を参照してください。

## <a name="1-create-an-existing-database"></a>1. 既存のデータベースを作成する

通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。

では、データベースを生成してみましょう。

-   Visual Studio を開きます
-   **ビュー&gt; サーバーエクスプローラー**
-   **[データ接続]** を右クリックし&gt; [接続の追加] をクリックします。
-   **サーバーエクスプローラー**からデータベースに接続していない場合は、データソースとして**Microsoft SQL Server**を選択する必要があります。

    ![データ ソースの選択](~/ef6/media/selectdatasource.png)

-   LocalDB インスタンスに接続し、データベース名として「**ブログ**」と入力します。

    ![LocalDB 接続](~/ef6/media/localdbconnection.png)

-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。

    ![データベースの作成ダイアログ](~/ef6/media/createdatabasedialog.png)

-   新しいデータベースがサーバーエクスプローラーに表示され、右クリックして **[新しいクエリ]** を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。

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

## <a name="2-create-the-application"></a>2. アプリケーションを作成する

単純にするために、Code First を使用してデータアクセスを行う基本的なコンソールアプリケーションを作成します。

-   Visual Studio を開きます
-   **ファイル&gt; 新規&gt; プロジェクト...**
-   左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。
-   名前として「 **Codefirstexistingを**入力してください」と入力します。
-   **[OK]** を選択します。

 

## <a name="3-reverse-engineer-model"></a>3. モデルのリバースエンジニアリング

ここでは、Visual Studio の Entity Framework Tools を使用して、データベースにマップする初期コードを生成します。 これらのツールはコードを生成するだけで、必要に応じて手動で入力することもできます。

-   **プロジェクト-新しい項目の追加&gt;...**
-   左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**
-   名前として「 **Bのログインコンテキスト**」と入力し、[ **OK]** をクリックします。
-   **Entity Data Model ウィザード**が起動します。
-   **データベースから Code First**を選択し、 **[次へ]** をクリックします。

    ![ウィザード1つの CFE](~/ef6/media/wizardonecfe.png)

-   最初のセクションで作成したデータベースへの接続を選択し、 **[次へ]** をクリックします。

    ![ウィザード 2 CFE](~/ef6/media/wizardtwocfe.png)

-   **[テーブル]** の横にあるチェックボックスをクリックしてすべてのテーブルをインポートし、 **[完了]** をクリックします。

    ![ウィザード 3 CFE](~/ef6/media/wizardthreecfe.png)

リバースエンジニアリングのプロセスが完了し、いくつかの項目がプロジェクトに追加されたら、追加された内容を見てみましょう。

### <a name="configuration-file"></a>構成ファイル

App.config ファイルがプロジェクトに追加されました。このファイルには、既存のデータベースへの接続文字列が含まれています。

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*構成ファイルには他にもいくつかの設定があります。これらは既定の EF 設定であり、データベースを作成する場所を Code First に指示します。既存のデータベースにマップしているため、この設定はアプリケーションでは無視されます。*

### <a name="derived-context"></a>派生コンテキスト

**B.log コンテキスト**クラスがプロジェクトに追加されました。 コンテキストは、データベースとのセッションを表します。これにより、データのクエリと保存が可能になります。
このコンテキストは、モデル内の各型の**Dbset&lt;TEntity&gt;** を公開します。 また、既定のコンストラクターは、 **name =** 構文を使用して基本コンストラクターを呼び出すことにも注意してください。 これは、このコンテキストに使用する接続文字列を構成ファイルから読み込む必要があることを Code First に伝えます。

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

*構成ファイルで接続文字列を使用する場合は、常に**name =** 構文を使用する必要があります。これにより、接続文字列が存在しない場合は、規則に従って新しいデータベースを作成するのではなく、Entity Framework がスローされます。*

### <a name="model-classes"></a>モデルクラス

最後に、**ブログ**と**Post**クラスもプロジェクトに追加されています。 これらは、モデルを構成するドメインクラスです。 クラスに適用されるデータ注釈が表示され、Code First の規則が既存のデータベースの構造と一致しない構成を指定します。 たとえば、Blog.Name と最大の最大長が**200**であるため、 **stringlength**注釈がと**Blog. Url**に表示されます Code First (既定では、データベースプロバイダー- **nvarchar (max SQL Server)** でサポートされているの長さを使用します)。

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

## <a name="4-reading--writing-data"></a>4. データの読み取り & 書き込み

モデルを作成したので、これを使用していくつかのデータにアクセスします。 次に示すように、 **Program.cs**に**Main**メソッドを実装します。 このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しい**ブログ**を挿入します。 次に、LINQ クエリを使用して、データベースのすべての**ブログ**を**タイトル**別にアルファベット順に並べ替えて取得します。

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

これで、アプリケーションを実行してテストできるようになりました。

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>データベースが変更した場合はどうなりますか?

データベースへの Code First ウィザードは、クラスの開始点セットを生成するように設計されています。このクラスを微調整して変更することができます。 データベーススキーマが変更された場合は、手動でクラスを編集するか、別のリバースエンジニアリングを実行してクラスを上書きすることができます。

## <a name="using-code-first-migrations-to-an-existing-database"></a>既存のデータベースへの Code First Migrations の使用

既存のデータベースで Code First Migrations を使用する場合は、「[既存のデータベースへの Code First Migrations](~/ef6/modeling/code-first/migrations/existing-database.md)」を参照してください。

## <a name="summary"></a>まとめ

このチュートリアルでは、既存のデータベースを使用した Code First 開発について説明しました。 Entity Framework Tools for Visual Studio を使用して、データベースにマップされた一連のクラスをリバースエンジニアリングし、データの格納と取得に使用しました。
