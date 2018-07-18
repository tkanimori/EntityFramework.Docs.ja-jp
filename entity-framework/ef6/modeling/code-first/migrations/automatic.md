---
title: 自動の Code First Migrations の EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
caps.latest.revision: 3
ms.openlocfilehash: 1f6ed728271e38d8e65fcf33fd45c74f085d9178
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122275"
---
# <a name="automatic-code-first-migrations"></a>自動の Code First Migrations
自動移行では、各変更のプロジェクト内のコード ファイルをしなくても Code First Migrations を使用できます。 すべての変更を自動的に適用できる - 列名の変更がコード ベースの移行の使用を要求するなど。

> [!NOTE]
> この記事では、基本的なシナリオで Code First Migrations を使用する方法を理解する前提としています。 そうしないかどうかを読み取る必要があります[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)続行する前にします。

## <a name="recommendation-for-team-environments"></a>チーム環境の推奨事項

自動およびコード ベースの移行を挿入することができますが、これは、チーム開発シナ リオでは推奨されません。 ソース管理を使用する開発者のチームに参加している場合のみ自動移行または純粋なコード ベースの移行が使用する必要があります。 特定の自動移行の制限事項、チーム環境でのコード ベースの移行の使用をお勧めします。

## <a name="building-an-initial-model--database"></a>初期モデルとデータベースをビルドする

移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。 このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。

-   新規作成**MigrationsAutomaticDemo**コンソール アプリケーション
-   プロジェクトに最新バージョンの **EntityFramework** NuGet パッケージを追加する
    -   **ツール -&gt; ライブラリ パッケージ マネージャー -&gt; パッケージ マネージャー コンソール**
    -   **Install-Package EntityFramework** コマンドを実行する
-   以下に示すコードで **Model.cs** ファイルを追加する。 このコードでは、ドメイン モデルを構成する 1 つの **Blog** クラスと EF Code First コンテキストである **BlogContext** クラスを定義します

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

      namespace MigrationsAutomaticDemo
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

-   アプリケーションを実行し、ことがわかりますが、 **MigrationsAutomaticCodeDemo.BlogContext**データベースが作成されます。

    ![DatabaseLocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>移行を有効にする

モデルにさらに変更を加えます。

-   Blog クラスに Url プロパティを追加します。

``` csharp
    public string Url { get; set; }
```

もう一度アプリケーションを実行すると、次を示す InvalidOperationException を取得します: *データベースの作成後、'BlogContext' コンテキストの背後にあるモデルが変更されました。Code First Migrations を使用したデータベースの更新を検討してください (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](http://go.microsoft.com/fwlink/?LinkId=238269)*)。*

例外で示されているように、Code First Migrations の使用を開始します。 自動移行を使用するため、ここを指定する、 **– EnableAutomaticMigrations**スイッチします。

-   実行、 **Enable-migrations – EnableAutomaticMigrations**パッケージ マネージャー コンソールのコマンドでコマンドが追加、**移行**フォルダー、プロジェクトにします。 この新しいフォルダーには、1 つのファイルが含まれています。

-   **構成クラス**。 このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。 このチュートリアルでは、既定の構成を使用します。
    *プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*

 

## <a name="your-first-automatic-migration"></a>最初の自動移行

Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。

-   **Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします
-   **Update-Database** では、保留中の移行をデータベースに適用します

回避するためにここでは (実際する必要があります) 場合を除き、追加の移行を使用して、Code First Migrations を自動的に通知することに焦点を計算し、変更を適用します。 使用しましょう**データベースを更新**私たちのモデルに変更をプッシュする Code First Migrations を取得する (新しい**Blog.Ur**l プロパティ)、データベースにします。

-   実行、 **Update-database**パッケージ マネージャー コンソール コマンド。

**MigrationsAutomaticDemo.BlogContext**に含めるデータベースが更新されるようになりました、 **Url**内の列、**ブログ**テーブル。

 

## <a name="your-second-automatic-migration"></a>2 番目の自動移行

別の変更し、Code First Migrations をデータベースに変更を自動的にプッシュできるようにしましょう。

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

使用して**Update-database**データベースの最新の状態にします。 今回は、Code First Migrations が実行している SQL を確認できるように、**–Verbose** フラグを指定します。

-   パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。

## <a name="adding-a-code-based-migration"></a>コードを追加するベースの移行

ここでのコード ベースの移行を使用する可能性が何かを見てみましょう。

-   追加、**評価**プロパティを**ブログ**クラス

``` csharp
    public int Rating { get; set; }
```

だけを実行できます**Update-database**データベースにこれらの変更をプッシュします。 ただし、追加しています、null 非許容**Blogs.Rating**列、テーブル内の既存のデータがある場合に割り当てる新しい列のデータ型の CLR 既定 (ことは、評価は、整数**0**). ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。
Add-migration コマンドを使用して、編集できるように、コード ベースの移行をアウトには、この変更を記述しましょう。 **Add-migration**コマンドで、これらの移行に名前を付け、という名前を付けましょう私たち**AddBlogRating**します。

-   実行、 **Add-migration AddBlogRating**パッケージ マネージャー コンソール コマンド。
-   **移行**フォルダーある新しい**AddBlogRating**移行します。 移行ファイル名は事前のタイムスタンプを持つ順序付けに関するヘルプを修正しました。 Blog.Rating (次のコード行 10) の 3 の既定値を指定する、生成されたコードを編集しましょう

*移行では、いくつかのメタデータをキャプチャする分離コード ファイルもあります。このメタデータは、このコード ベースの移行の前に実行された自動移行をレプリケートする Code First Migrations をことができます。これは、機能は、他の開発者が、移行を実行する場合、または、アプリケーションをデプロイするときに重要です。*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

編集した移行は問題なさそうなので、**Update-Database** を使用してデータベースを最新の状態にします。

-   実行、 **Update-database**パッケージ マネージャー コンソール コマンド。

## <a name="back-to-automatic-migrations"></a>自動移行に戻る

単純な変更の自動移行に戻るに空きができました。 Code First Migrations は、自動およびコード ベースの移行を実行する各コード ベースの移行の分離コード ファイルに保存するが、メタデータに基づいて、正しい順序で行われます。

-   Post.Abstract プロパティをモデルに追加しましょう

``` csharp
    public string Abstract { get; set; }
```

使用できるようになりました**Update-database**自動移行を使用してデータベースにこの変更をプッシュする Code First Migrations を取得します。

-   実行、 **Update-database**パッケージ マネージャー コンソール コマンド。

## <a name="summary"></a>まとめ

自動移行を使用してプッシュする方法を説明するこのチュートリアルでは、モデルは、データベースに変更されます。 スキャフォールディングし、詳細に制御する必要がある場合は、自動移行の間のコード ベースの移行を実行する方法も説明しました。
