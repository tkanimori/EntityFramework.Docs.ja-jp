---
title: 新しいデータベースへの Code First-EF6
description: Entity Framework 6 の新しいデータベースへの Code First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065020"
---
# <a name="code-first-to-a-new-database"></a>新しいデータベースの Code First
このビデオとステップバイステップのチュートリアルでは、新しいデータベースを対象とした Code First 開発の概要について説明します。 このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。 Code First では、C または VB.Net クラスを使用してモデルを定義でき \# ます。 追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、新しいデータベースを対象とした Code First 開発の概要について説明します。 このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。 Code First を使用すると、C# または VB.Net クラスを使用してモデルを定義できます。 追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。

**提供**: [Rowan Miller](https://romiller.com/)

**ビデオ**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) もインストールする必要があります。

## <a name="1-create-the-application"></a>1. アプリケーションを作成する

単純にするために、Code First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。

-   Visual Studio を開く
-   **ファイル- &gt; 新規 &gt; プロジェクト...**
-   左側のメニューと**コンソールアプリケーション**から [ **Windows** ] を選択します。
-   名前として「 **Codefirstnewconnectionstring** 」と入力します。
-   **[OK]** を選択します。

## <a name="2-create-the-model"></a>2. モデルを作成する

クラスを使用して、非常に単純なモデルを定義してみましょう。 これらは Program.cs ファイルで定義するだけですが、実際のアプリケーションでは、クラスを別々のファイルに分割し、場合によっては別のプロジェクトに分割します。

Program.cs の Program クラス定義の下に、次の2つのクラスを追加します。

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

2つのナビゲーションプロパティ (ブログ、投稿、ブログ) が作成されていることがわかります。 これにより、Entity Framework の遅延読み込み機能が有効になります。 遅延読み込みとは、これらのプロパティにアクセスしようとしたときに、それらのプロパティの内容がデータベースから自動的に読み込まれることを意味します。

## <a name="3-create-a-context"></a>3. コンテキストを作成する

次に、データベースとのセッションを表す派生コンテキストを定義します。これにより、データのクエリと保存が可能になります。 ここでは、TEntity から派生し、 &lt; &gt; モデル内のクラスごとに型指定された dbcontext を公開するコンテキストを定義します。

ここでは、Entity Framework の型の使用を開始しているため、EntityFramework NuGet パッケージを追加する必要があります。

-   **プロジェクト– &gt; NuGet パッケージの管理...**
    注: [ **NuGet パッケージの管理...** オプション[NuGet の最新バージョン](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があります
-   [ **オンライン** ] タブを選択します。
-   **Entityframework**パッケージを選択します
-   **[Install]** (インストール) をクリックします。

Program.cs の先頭に、using ステートメントを追加します。

``` csharp
using System.Data.Entity;
```

Program.cs の Post クラスの下に、次の派生コンテキストを追加します。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

ここでは、Program.cs に含まれる内容の完全な一覧を示します。

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

これは、データの格納と取得を開始するために必要なすべてのコードです。 当然ながら、背後にはかなりの時間がかかりますが、それについては後で説明しますが、実際にはそれを実際に見てみましょう。

## <a name="4-reading--writing-data"></a>4. データの読み取り & 書き込み

次に示すように、Program.cs に Main メソッドを実装します。 このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。 次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。

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
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>データはどこにありますか?

慣例により、DbContext によってデータベースが作成されました。

-   ローカルの SQL Express インスタンスが使用可能な場合 (既定では Visual Studio 2010 がインストールされている場合)、Code First によってそのインスタンスにデータベースが作成されます。
-   SQL Express が使用できない場合、Code First は [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) を使用します (既定では Visual Studio 2012 と共にインストールされます)。
-   データベースには、派生コンテキストの完全修飾名の後に名前が付けられます。この例では、 **Codefirstnewbfile です。**

これらは既定の規則であり、Code First 使用するデータベースを変更するにはさまざまな方法があります。詳細については、「 **DbContext がモデルとデータベースの接続を検出する方法** 」を参照してください。
このデータベースに接続するには、Visual Studio のサーバーエクスプローラーを使用します。

-   **ビュー- &gt; サーバーエクスプローラー**
-   [**データ接続**] を右クリックし、[**接続の追加**] を選択します。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。

    ![データ ソースを選択する](~/ef6/media/selectdatasource.png)

-   インストールされているものに応じて、LocalDB または SQL Express に接続します。

Code First 作成されたスキーマを調査できるようになりました。

![スキーマの初期](~/ef6/media/schemainitial.png)

DbContext は、定義した Dbcontext プロパティを参照して、モデルに含めるクラスを処理しました。 次に、Code First 規則の既定のセットを使用して、テーブル名と列名の決定、データ型の確認、主キーの検索などを行います。 このチュートリアルの後半では、これらの規則をオーバーライドする方法について説明します。

## <a name="5-dealing-with-model-changes"></a>5. モデルの変更を処理する

次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。 これを行うには、Code First Migrations、または短い間の移行と呼ばれる機能を使用します。

移行により、データベーススキーマをアップグレード (およびダウングレード) する方法を説明する一連の手順を実行できます。 これらの各手順 (移行と呼ばれます) には、適用される変更を説明するコードが含まれています。 

最初の手順では、BCode First Migrations コンテキストのを有効にします。

-   **ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソール**
-   パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します
-   次の2つの項目を含む新しい [移行] フォルダーがプロジェクトに追加されました。
    -   **Configuration.cs** –このファイルには、b コンテキストの移行に使用する設定が含まれています。 このチュートリアルでは何も変更する必要はありませんが、ここでは、シードデータを指定したり、他のデータベースのプロバイダーを登録したり、移行が生成される名前空間を変更したりできます。
    -   ** &lt; timestamp &gt; \_ InitialCreate.cs** –これは最初の移行です。これは、データベースに既に適用されている変更を表し、ブログと投稿テーブルを含むデータベースに対して空のデータベースからの変更を行います。 これらのテーブルは Code First によって自動的に作成されるようになっていますが、移行に移行された移行を選択しました。 Code First は、この移行が既に適用されているローカルデータベースにも記録されています。 ファイル名のタイムスタンプは、順序付けの目的で使用されます。

    次に、モデルに変更を加え、ブログクラスに Url プロパティを追加します。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   パッケージマネージャーコンソールで、[ **追加-移行] AddUrl** コマンドを実行します。
    Add-Migration コマンドは、前回の移行以降に行われた変更を確認し、見つかった変更を含む新しい移行をスキャフォールディングします。 移行に名前を付けることができます。この例では、移行 ' AddUrl ' を呼び出しています。
    スキャフォールディングコードは、文字列データを保持できる Url 列を dbo に追加する必要があることを示しています。ブログテーブル。 必要に応じて、スキャフォールディングコードを編集することもできますが、この場合は必要ありません。

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

-   パッケージマネージャーコンソールで、 **データベースの更新** コマンドを実行します。 このコマンドを実行すると、保留中の移行がデータベースに適用されます。 InitialCreate 移行は既に適用されているので、移行によって新しい AddUrl 移行が適用されます。
    ヒント: Update-Database を呼び出すときに **– Verbose** スイッチを使用して、データベースに対して実行される SQL を確認できます。

これで、新しい Url 列がデータベースのブログテーブルに追加されました。

![Url を含むスキーマ](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. データ注釈

ここまでで、EF では既定の規則を使用してモデルを検出できるようになりましたが、クラスが規則に従わず、さらに構成を実行できるようにする必要があります。 これには2つのオプションがあります。このセクションのデータ注釈については、次のセクションの fluent API を参照してください。

-   モデルにユーザークラスを追加してみましょう。

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   また、派生コンテキストにセットを追加する必要もあります。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   移行を追加しようとすると、"*EntityType ' User ' にキーが定義されていません" というエラーが表示されます。この EntityType のキーを定義します。 "* EF では、ユーザー名がユーザーの主キーであることを把握する方法がありません。
-   ここではデータ注釈を使用します。そのため、Program.cs の先頭に using ステートメントを追加する必要があります。

```csharp
using System.ComponentModel.DataAnnotations;
```

-   次に、ユーザー名プロパティに注釈を付けて、それが主キーであることを識別します。

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   スキャフォールディングを使用 **して移行を実行し、** これらの変更をデータベースに適用します。
-   新しい移行をデータベースに適用するには、 **データベースの更新** コマンドを実行します。

これで、新しいテーブルがデータベースに追加されました。

![ユーザーを使用したスキーマ](~/ef6/media/schemawithusers.png)

EF でサポートされている注釈の完全な一覧は次のとおりです。

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [Stringlength 属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [タイムスタンプ属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. Fluent API

前のセクションでは、データ注釈を使用して、規則によって検出された内容を補完またはオーバーライドする方法を説明しました。 モデルを構成するもう1つの方法は、Code First fluent API を使用することです。

ほとんどのモデル構成は、単純なデータ注釈を使用して実行できます。 Fluent API は、データ注釈では実現できない高度な構成に加えて、データ注釈が実行できるすべてのものを網羅したモデル構成を指定するより高度な方法です。 データ注釈と fluent API を一緒に使用することができます。

Fluent API にアクセスするには、DbContext で OnModelCreating メソッドをオーバーライドします。 たとえば、ユーザー DisplayName が格納されている列の名前を表示名に変更したいとします \_ 。

-   次のコードを使用して、BloggingContext で OnModelCreating メソッドをオーバーライドします。

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

-   スキャフォールディングを使用 **して移行を実行し、** これらの変更をデータベースに適用します。
-   新しい移行をデータベースに適用するには、 **データベースの更新** コマンドを実行します。

DisplayName 列の名前が表示名に変更されました \_ 。

![表示名が変更されたスキーマ](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>まとめ

このチュートリアルでは、新しいデータベースを使用した Code First 開発について説明しました。 クラスを使用してモデルを定義した後、そのモデルを使用してデータベースを作成し、データを格納および取得します。 データベースを作成した後は、モデルの進化に従ってスキーマを変更するために Code First Migrations を使用しました。 また、データ注釈と Fluent API を使用してモデルを構成する方法についても説明しました。
