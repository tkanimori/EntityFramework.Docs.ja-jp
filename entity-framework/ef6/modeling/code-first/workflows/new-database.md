---
title: 新しいデータベース - EF6 code First
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: a19db575b685cde98509fff4a0efaf26106b26bc
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284123"
---
# <a name="code-first-to-a-new-database"></a>新しいデータベースの code First
このビデオ、およびステップ バイ ステップ チュートリアルでは、新しいデータベースを対象とする Code First の開発の概要を提供します。 このシナリオには、存在しないデータベースを対象とするが含まれていますと Code First が作成、または空のデータベースを Code First が新しいテーブルを追加します。 コードを最初に使用すると、C を使用して、モデル定義\#または VB.Net クラス。 必要に応じてクラスやプロパティ、または fluent API を使用して属性を使用して追加の構成を実行できます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、新しいデータベースを対象とする Code First の開発の概要を提供します。 このシナリオには、存在しないデータベースを対象とするが含まれていますと Code First が作成、または空のデータベースを Code First が新しいテーブルを追加します。 まず、コードを使用すると、C# または VB.Net のクラスを使用してモデルを定義できます。 必要に応じてクラスやプロパティ、または fluent API を使用して属性を使用して追加の構成を実行できます。

**提供**: [Rowan Miller](http://romiller.com/)

**ビデオ**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>前提条件

少なくとも Visual Studio 2010 が必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。

Visual Studio 2010 を使用している場合も必要になりますが[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。

## <a name="1-create-the-application"></a>1.アプリケーションを作成する

物事を複雑に Code First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションの構築になります。

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のメニューと**コンソール アプリケーション**
-   入力**CodeFirstNewDatabaseSample**名として
-   **[OK]** を選択します。

## <a name="2-create-the-model"></a>2.モデルを作成します。

クラスを使用して、非常に単純なモデルを定義してみましょう。 定義していますだけに、Program.cs ファイルが個別のファイルや可能性のある別のプロジェクトをクラスに分割するが、実際のアプリケーションにします。

Program.cs で、プログラムのクラス定義の下には、次の 2 つのクラスを追加します。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

行って 2 つのナビゲーション プロパティ (Blog.Posts および Post.Blog) 仮想がわかります。 これにより、Entity Framework の遅延読み込み機能です。 遅延読み込みでは、それらにアクセスするときに、これらのプロパティの内容がデータベースから自動的に読み込まれることを意味します。

## <a name="3-create-a-context"></a>3.コンテキストを作成します。

ここには、クエリとデータを保存することができます、データベースとのセッションを表す、派生コンテキストの定義します。 System.Data.Entity.DbContext から派生し、型指定された DbSet を公開するコンテキストを定義します&lt;TEntity&gt;私たちのモデルの各クラスに対して。

今すぐ EntityFramework NuGet パッケージを追加する必要があります、Entity Framework の型を使用を開始しています。

-   **プロジェクト:&gt; NuGet パッケージを管理しています.**
    注: していない場合、 **NuGet パッケージの管理.** オプションをインストールする必要があります、 [NuGet の最新バージョン](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   選択、**オンライン** タブ
-   選択、 **EntityFramework**パッケージ
-   クリックして**インストール**

使用して、追加の Program.cs の上部にある System.Data.Entity ステートメント。

``` csharp
using System.Data.Entity;
```

投稿の下は、Program.cs 内のクラスは、次の派生コンテキストを追加します。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

Program.cs を今すぐに含められるものの完全な一覧を次に示します。

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

格納して、データの取得を開始する必要があります。 すべてのコードです。 当然ながら、バック グラウンドで起こっている非常にビットがあるし、見ていきますにあることですが、まずこの後すぐにみましょう動作を確認します。

## <a name="4-reading--writing-data"></a>4.読み取りとデータの書き込み

次に示すように、Program.cs の Main メソッドを実装します。 このコードは、コンテキストの新しいインスタンスを作成、し、それを使用して、新しいブログを挿入します。 タイトルでアルファベット順では、データベースからすべてのブログを取得するのに LINQ クエリを使用します。

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
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>データがあるでしょうか。

規則により、DbContext がデータベースを作成しました。

-   ローカル SQL Express のインスタンスが使用可能な (Visual Studio 2010 では既定でインストールされている) 場合、Code First が作成、データベース インスタンス上
-   SQL Express を使用できないかどうか、Code First を使用して[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (Visual Studio 2012 では、既定でインストール)
-   今回の場合に、派生コンテキストの完全修飾名の後に、データベースの名前は**CodeFirstNewDatabaseSample.BloggingContext**

これらは、既定の規則と Code First を使用するデータベースを変更するさまざまな方法が、詳細については、**モデルとデータベース接続の DbContext の検出**トピック。
Visual Studio でサーバー エクスプ ローラーを使用してこのデータベースに接続することができます。

-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続**選択**接続の追加.**
-   場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。

    ![データ ソースの選択](~/ef6/media/selectdatasource.png)

-   LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。

Code First を作成したスキーマを検査できるようになりました。

![初期のスキーマ](~/ef6/media/schemainitial.png)

DbContext を出して DbSet プロパティ定義を調べることで、モデルに含めるには、どのようなクラスです。 Code First の規則の既定のセットを使用して、テーブルと列名を決定する、データ型を決定する、主キーなどを検索します。 このチュートリアルの後半では、これらの規則を無効にする方法を紹介します。

## <a name="5-dealing-with-model-changes"></a>5.モデルの変更を処理します。

ここには、データベース スキーマを更新する必要もありますこれらの変更を行ったときに、モデルでは、変更を加えます。 これを行うには、Code First Migrations、または移行を略してと呼ばれる機能を使用するでしょう。

移行により、データベース スキーマをアップグレード (およびダウン グレード) する方法について説明する手順の順序付けされたセットを作成します。 次の手順は、移行と呼ばれるには、適用する変更について説明したいくつかのコードが含まれています。 

最初の手順では、BloggingContext を Code First Migrations を有効にします。

-   **ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**
-   パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します
-   新しい Migrations フォルダーが 2 つの項目を含む、プロジェクトに追加されています。
    -   **Configuration.cs** – このファイルには、移行するための移行を使用する設定が含まれています。 BloggingContext します。 このチュートリアルでは何も変更する必要はありませんが、ここでは、シード データを他のデータベースで登録プロバイダー名前空間の変更を指定することができますなどで、移行が生成されることです。
    -   **&lt;タイムスタンプ&gt;\_InitialCreate.cs** – これは、最初の移行、ブログや投稿のテーブルを含む 1 つに空のデータベースからをデータベースに既に適用されている変更を表します. Code First からへの移行、移行に変換されたことにしましたところ、これらのテーブルを自動的に作成できるようにします。 コードは、まず、この移行が既に適用されているローカル データベースには記録もします。 ファイル名のタイムスタンプは、並べ替えの目的で使用されます。

    これで、モデルに変更を加える、ブログ クラスに Url プロパティを追加しましょう。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   実行、 **Add-migration AddUrl**パッケージ マネージャー コンソール コマンド。
    Add-migration コマンドは、最後の移行後の変更を確認し、検出したすべての変更と新しい移行をスキャフォールディングします。 移行は名を紹介します。この場合、移行 'AddUrl' と呼んでいます。
    スキャフォールディングされたコードは、dbo に文字列データを保持できる、Url の列を追加する必要があることを示しています。ブログのテーブルです。 必要な場合は、スキャフォールディングされたコードを編集しましたでしたがここでする必要はありません。

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   実行、 **Update-database**パッケージ マネージャー コンソール コマンド。 このコマンドは、保留中の移行をデータベースに適用されます。 InitialCreate 移行は、移行は、新しい AddUrl 移行のみ適用ために既に適用されています。
    ヒント: が使用できる、 **– Verbose**データベースに対して実行されている SQL を表示するには、Update-database を呼び出すときに切り替えます。

データベースのブログ テーブルには、新しい Url 列は追加がようになりました。

![Url のスキーマ](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6.データの注釈

だけした EF の既定の規約を使用して、モデルを検出できるようにこれまでがあるとなる時間とクラスは、規則に従っていないし、詳細な構成を実行できる必要があります。 は、この 2 つのオプションがあります。このセクションではデータ注釈とし、次のセクションで、fluent API に注目します。

-   私たちのモデルに追加するユーザー クラス

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   また、派生コンテキストにセットを追加する必要があります。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   というエラー メッセージがこのように移行を追加しようとすると"*'User' の EntityType にキーが定義されていません。この EntityType にキーを定義します。"* EF には、ユーザー名のユーザーのプライマリ キーがあることを知る方法がありません。
-   今すぐデータ注釈を使用して、使用して、追加する必要がありますを Program.cs の上部にあるステートメント

```
using System.ComponentModel.DataAnnotations;
```

-   主キーであるかを識別するためにユーザー名プロパティに注釈を付けるようになりました

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   使用して、 **Add-migration AddUser**データベースに変更を適用これらへの移行をスキャフォールディングするコマンド
-   実行、 **Update-database**データベースに新しい移行を適用するコマンド

新しいテーブルがデータベースに追加ようになりました。

![ユーザーとスキーマ](~/ef6/media/schemawithusers.png)

EF によってサポートされている注釈の完全な一覧です。

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7.Fluent API

前のセクションでを補完したり、規則によって検出されたものをオーバーライドするデータの注釈の使用について説明しました。 モデルを構成するその他の方法は、Code First fluent API を使用してです。

ほとんどのモデル構成には単純なデータの注釈を使用します。 Fluent API は、データ注釈は、データ注釈では実現できないいくつかのより高度な構成をさらに実行できる情報を網羅したモデルの構成を指定するより高度な方法です。 データ注釈と fluent API を一緒に使用することができます。

Fluent API にアクセスするには、DbContext で OnModelCreating メソッドをオーバーライドします。 表示するに User.DisplayName が格納されている列の名前を変更したいと\_名。

-   次のコードで BloggingContext で OnModelCreating メソッドをオーバーライドします。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   使用して、 **Add-migration ChangeDisplayName**コマンドは、これらを適用する移行をスキャフォールディングするデータベースを変更します。
-   実行、 **Update-database**データベースに新しい移行を適用するコマンド。

DisplayName 列の名前が表示にようになりました\_名。

![スキーマの名前を変更する表示名](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>まとめ

このチュートリアルでは、新しいデータベースを使用して Code First の開発に説明しました。 私たちは、クラスを使用してモデルを定義し、そのモデル データベースを作成し、格納およびデータを取得するために使用します。 データベースが作成された後 Code First Migrations を使用して、進化、モデルとスキーマを変更しました。 また、データ注釈と Fluent API を使用してモデルを構成する方法を説明しました。
