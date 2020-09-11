---
title: Code First Migrations - EF6
description: Entity Framework 6 での Code First Migrations
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: b2200a91b087cf6f2dff0803b710e2518193af40
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616962"
---
# <a name="code-first-migrations"></a>Code First Migrations
Code First Migrations は、Code First ワークフローを使用している場合に、アプリケーションのデータベース スキーマを進化させるために推奨される方法です。 移行では次を許可するツール セットを提供します。

1. EF モデルを使用する初期データベースを作成する
2. EF モデルに行った変更の記録を保持するために移行を生成する
2. これらの変更でデータベースを最新の状態に保つ

次のチュートリアルでは、Entity Framework での Code First Migrations の概要を示します。 チュートリアル全体を完了するか、関心のあるトピックにスキップするかのいずれかを行うことができます。 次のトピックが含まれています。

## <a name="building-an-initial-model--database"></a>初期モデルとデータベースをビルドする

移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。 このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。

-   新しい **MigrationsDemo** コンソール アプリケーションを作成する
-   プロジェクトに最新バージョンの **EntityFramework** NuGet パッケージを追加する
    -   **ツール -&gt; ライブラリ パッケージ マネージャー -&gt; パッケージ マネージャー コンソール**
    -   **Install-Package EntityFramework** コマンドを実行する
-   以下に示すコードで **Model.cs** ファイルを追加する。 このコードでは、ドメイン モデルを構成する 1 つの **Blog** クラスと EF Code First コンテキストである **BlogContext** クラスを定義します

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   これでモデルが用意できました。モデルを使ってデータ アクセスを実行してみましょう。 **Program.cs** ファイルを以下のコードで更新します。

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   アプリケーションを実行すると、**MigrationsCodeDemo.BlogContext** データベースが作成されたことが表示されます。

    ![データベース LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>移行を有効にする

モデルにさらに変更を加えます。

-   Blog クラスに Url プロパティを追加します。

``` csharp
    public string Url { get; set; }
```

もう一度アプリケーションを実行すると、次を示す InvalidOperationException を取得します: *データベースの作成後、'BlogContext' コンテキストの背後にあるモデルが変更されました。Code First Migrations を使用したデータベースの更新を検討してください (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*

例外で示されているように、Code First Migrations の使用を開始します。 最初の手順では、コンテキストの移行を有効にします。

-   パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します

    このコマンドでは、**Migrations** フォルダーをプロジェクトに追加します。 この新しいフォルダーには、次の 2 つのファイルが含まれます。

-   **構成クラス**。 このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。 このチュートリアルでは、既定の構成を使用します。
    *プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*
-   **InitialCreate 移行**。 移行を有効にする前に、既に Code First でデータベースを作成しているため、この移行が生成されました。 このスキャフォールディングされた移行でのコードは、データベース内に既に作成されているオブジェクトを表しています。 このケースでは、**BlogId** 列と **Name** 列を含む **Blog** テーブルです。 ファイル名には、並べ替えに役立つタイムスタンプが含まれます。
    *データベースがまだ作成されていない場合、この InitialCreate 移行がプロジェクトに追加されることはありません。代わりに、最初に Add-Migration を呼び出し、これらのテーブルを作成するコードは、新しい移行に対してスキャフォールディングされます。*

### <a name="multiple-models-targeting-the-same-database"></a>同じデータベースを対象とする複数のモデル

EF6 より前のバージョンを使用している場合、データベースのスキーマを生成または管理するために、1 つの Code First モデルのみを使用できます。 これは、どのエントリがどのモデルに属するかを識別することができないデータベースごとの 1 つの **\_\_MigrationsHistory** テーブルの結果です。

EF6 以降、**Configuration** クラスには **ContextKey** プロパティが含まれます。 これは、Code First モデルごとに一意識別子として指定されます。 **\_\_MigrationsHistory** テーブルの対応する列には、このテーブルを共有する複数のモデルからのエントリを許可します。 既定では、このプロパティにはコンテキストの完全修飾名が設定されます。

## <a name="generating--running-migrations"></a>移行を生成して実行する

Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。

-   **Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします
-   **Update-Database** では、保留中の移行をデータベースに適用します

追加した新しい Url プロパティを処理するには、移行をスキャフォールディングする必要があります。 **Add-Migration** コマンドを使用すると、これらの移行に名前を付けることができます。ここでは、**AddBlogUrl** という名前を付けます。

-   パッケージ マネージャー コンソールで **Add-Migration AddBlogUrl** コマンドを実行します
-   **Migrations** フォルダーに、新しい **AddBlogUrl** 移行ができました。 移行のファイル名の先頭には、並べ替えに役立つタイムスタンプが付きます

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

この移行を編集したり、この移行に追加したりすることができますが、すべてが適切なようです。 **Update-Database** を使って、この移行をデータベースに適用します。

-   パッケージ マネージャー コンソールで **Update-Database** コマンドを実行します
-   Code First Migrations では、**Migrations** フォルダー内の移行と、データベースに適用されている移行が比較されます。 **AddBlogUrl** 移行を適用する必要があることがわかるため、これを実行します。

これで、**MigrationsDemo.BlogContext** データベースが、**Blogs** テーブル内の **Url** 列を含めるように更新されました。

## <a name="customizing-migrations"></a>移行をカスタマイズする

これまで、変更を加えずに移行を生成して実行しています。 既定で生成されたコードの編集について見てみましょう。

-   モデルにさらに変更を加えます。新しい **Rating** プロパティを **Blog** クラスに追加しましょう

``` csharp
    public int Rating { get; set; }
```

-   新しい **Post** クラスも追加します

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   **Blog** と **Post** の間のリレーションシップのもう一方を形成するために、**Blog** クラスに **Posts** コレクションも追加します

``` csharp
    public virtual List<Post> Posts { get; set; }
```

移行時に Code First Migrations によってその最適な推量をスキャフォールディングできるように、**Add-Migration** コマンドを使用します。 この移行を **AddPostClass** と呼びます。

-   パッケージ マネージャー コンソールで **Add-Migration AddPostClass** コマンドを実行します。

Code First Migrations では、これらの変更のスキャフォールディングを適切に処理していますが、変更が必要な可能性がある内容がいくつかあります。

1.  最初に、(以下のコードに、行 22 と行 29 を追加して) 一意のインデックスを **Posts.Title** 列に追加します。
2.  また、NULL 非許容の **Blogs.Rating** 列も追加します。 テーブルに既存のデータがある場合は、新しい列に CLR の既定のデータの種類を割り当てます (Rating が整数の場合、**0** になります)。 ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。
    (以下のコードの行 24 で指定した既定値を確認できます)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

編集済みの移行の準備ができたので、**Update-Database** を使用してデータベースを最新の状態にします。 今回は、Code First Migrations が実行している SQL を確認できるように、**–Verbose** フラグを指定します。

-   パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。

## <a name="data-motion--custom-sql"></a>データ モーション / カスタム SQL

これまでデータを変更または移動を行わない移行操作を見てきました。ここでは、一部のデータを移動させる必要がある内容を見てみましょう。 まだデータ モーション用のネイティブ サポートはありませんが、スクリプトの任意の時点でいくつかの任意の SQL コマンドを実行できます。

-   **Post.Abstract** プロパティをモデルに追加します。 後で、**Content** 列の先頭から一部のテキストを使用して、既存の投稿用に **Abstract** を事前に設定します。

``` csharp
    public string Abstract { get; set; }
```

移行時に Code First Migrations によってその最適な推量をスキャフォールディングできるように、**Add-Migration** コマンドを使用します。

-   パッケージ マネージャー コンソールで **Add-Migration AddPostAbstract** コマンドを実行します。
-   生成された移行でスキーマの変更が行われますが、投稿ごとにコンテンツの最初の 100 文字を使用して、**Abstract** 列を事前に設定する必要もあります。 列が追加された後、SQL にドロップ ダウンして **UPDATE** ステートメントを実行することにより、この操作を行うことができます。
    (以下のコードの行 12 に追加します)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

編集した移行は問題なさそうなので、**Update-Database** を使用してデータベースを最新の状態にします。 このデータベースに対して実行を開始する SQL を確認できるように、**–Verbose** フラグを指定します。

-   パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。

## <a name="migrate-to-a-specific-version-including-downgrade"></a>特定のバージョンへの移行 (ダウングレードを含む)

これまでは常に、最新の移行にアップグレードしてきましたが、特定の移行にアップグレードまたはダウングレードしたい場合があります。

**AddBlogUrl** 移行を実行した後、データベースを以前の状態に移行する必要があるとします。 **–TargetMigration** スイッチを使用して、この移行にダウングレードすることができます。

-   パッケージ マネージャー コンソールで **Update-Database –TargetMigration: AddBlogUrl** コマンドを実行します。

このコマンドでは、**AddBlogAbstract** 移行と **AddPostClass** 移行に対して Down スクリプトが実行されます。

空のデータベースに完全にロール バックする場合は、**Update-Database –TargetMigration: $InitialDatabase** コマンドを使用できます。

## <a name="getting-a-sql-script"></a>SQL スクリプトを取得する

別の開発者が使用しているマシン上でこれらの変更が必要な場合は、ソース管理への変更を確認したときに同期することができます。 新しい移行が用意できたら、Update-Database コマンドを実行するだけで、変更をローカルに適用できます。 ただし、これらの変更をテスト サーバーにプッシュし、最終的に実稼働環境のサーバーにプッシュする必要がある場合は、DBA にハンドオフできる SQL スクリプトが必要な場合があります。

-   **Update-Database** コマンドを実行しますが、今回は **–Script** フラグを指定するため、変更は適用されずにスクリプトに書き込まれます。 スクリプトを生成するソース移行とターゲット移行も指定します。 空のデータベース (**$InitialDatabase**) から最新バージョン (移行 **AddPostAbstract**) に移動するスクリプトが必要です。
    *ターゲット移行を指定しない場合、Migrations ではターゲットとして最新の移行を使用します。ソース移行を指定しない場合は、Migrations ではデータベースの現在の状態を使用します。*
-   パッケージ マネージャー コンソールで **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** コマンドを実行します

Code First Migrations では、実際に変更を適用するのではなく、移行パイプラインを実行しますが、これにより変更が .sql ファイルに書き込まれます。 スクリプトが生成されると、表示または保存できるように、Visual Studio で開かれます。

### <a name="generating-idempotent-scripts"></a>べき等スクリプトを生成する

EF6 以降、**–SourceMigration $InitialDatabase** を指定した場合、生成されたスクリプトは "べき等" になります。 べき等スクリプトでは、任意のバージョンのデータベースを最新バージョン (または、**–TargetMigration** を使用する場合、指定されたバージョン) にアップグレードできます。 生成されたスクリプトには、 **\_\_MigrationsHistory** テーブルを確認し、以前に適用されている変更のみを適用するロジックが含まれます。

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>アプリケーションの起動で自動的にアップグレードする (MigrateDatabaseToLatestVersion 初期化子)

アプリケーションをデプロイしている場合は、アプリケーションの起動時に、(保留中の移行を適用して) データベースを自動的にアップグレードするために必要なことがあります。 **MigrateDatabaseToLatestVersion** データベース初期化子を登録して、この操作を行うことができます。 データベース初期化子には、単にデータベースが正しく設定されていることを確認するために使用されるいくつかのロジックが含まれています。 このロジックは、最初にコンテキストがアプリケーション プロセス (**AppDomain**) 内で使用されるときに実行されます。

以下に示すように、**Program.cs** ファイルを更新して、コンテキストを使用する前に BlogContext に **MigrateDatabaseToLatestVersion** 初期化子を設定できます (行 14)。 **System.Data.Entity** 名前空間の using ステートメント (行 5) も追加する必要があることに注意してください。

*この初期化子のインスタンスを作成した場合、コンテキストの種類 (**BlogContext**) と移行の構成 (**Configuration**) を指定する必要があります。移行の構成は、移行を有効にしたときの **Migrations** フォルダーに追加されたクラスです。*

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

これで、アプリケーションが実行されるたびに、最初にターゲットにしているデータベースが最新かどうかを確認し、最新でない場合は保留中の移行を適用するようになりました。
