---
title: 自動 Code First Migrations-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415701"
---
# <a name="automatic-code-first-migrations"></a>自動 Code First Migrations
自動移行を使用すると、変更ごとにプロジェクトにコードファイルを作成しなくても Code First Migrations を使用できます。 すべての変更を自動的に適用できるわけではありません。たとえば、列の名前を変更するには、コードベースの移行を使用する必要があります。

> [!NOTE]
> この記事では、基本的なシナリオでの Code First Migrations の使用方法を理解していることを前提としています。 そうでない場合は、続行する前に[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)を読む必要があります。

## <a name="recommendation-for-team-environments"></a>チーム環境の推奨事項

自動およびコードベースの移行を intersperse することはできますが、これはチーム開発シナリオでは推奨されません。 ソース管理を使用する開発者チームの一員である場合は、純粋な自動移行または純粋なコードベースの移行を使用する必要があります。 自動移行の制限を考慮して、チーム環境でコードベースの移行を使用することをお勧めします。

## <a name="building-an-initial-model--database"></a>初期モデルとデータベースをビルドする

移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。 このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。

-   新しい**MigrationsAutomaticDemo**コンソールアプリケーションを作成する
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

-   アプリケーションを実行すると、 **MigrationsAutomaticCodeDemo コンテキスト**データベースが作成されていることがわかります。

    ![データベース LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>移行を有効にする

モデルにさらに変更を加えます。

-   Blog クラスに Url プロパティを追加します。

``` csharp
    public string Url { get; set; }
```

アプリケーションをもう一度実行すると、*データベースの作成後に ' ブログのコンテキスト ' コンテキストが変更されたモデルを示す InvalidOperationException が表示されます。Code First Migrations を使用してデータベースを更新することを検討してください (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*

例外で示されているように、Code First Migrations の使用を開始します。 自動移行を使用するため、 **– enable自動移行**スイッチを指定します。

-   パッケージマネージャーコンソールで、 **[移行の有効化-Enable自動移行]** コマンドを実行します。このコマンドにより、 **[移行]** フォルダーがプロジェクトに追加されました。 この新しいフォルダーには、次のファイルが1つ含まれています:

-   **構成クラス**。 このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。 このチュートリアルでは、既定の構成を使用します。
    *プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*

 

## <a name="your-first-automatic-migration"></a>最初の自動移行

Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。

-   **Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします
-   **Update-Database** では、保留中の移行をデータベースに適用します

(本当に必要な場合を除き) 追加移行を使用しないようにし、Code First Migrations によって自動的に計算され、変更が適用されるようにします。 **更新データベース**を使用して、モデル (新しい**Blog. Ur**l プロパティ) への変更をデータベースにプッシュ Code First Migrations を取得してみましょう。

-   パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。

これで、 **MigrationsAutomaticDemo コンテキスト**データベースが更新され、**ブログ**テーブルに**Url**列が含まれるようになりました。

 

## <a name="your-second-automatic-migration"></a>2回目の自動移行

別の変更を加えて、Code First Migrations によってデータベースに変更が自動的にプッシュされるようにしましょう。

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

次に、データベースを最新の状態にするために、データベースを**更新**します。 今回は、Code First Migrations が実行している SQL を確認できるように、 **–Verbose** フラグを指定します。

-   パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。

## <a name="adding-a-code-based-migration"></a>コードベースの移行の追加

では、コードベースの移行を使用する必要があるものを見てみましょう。

-   **評価**プロパティを**ブログ**クラスに追加してみましょう

``` csharp
    public int Rating { get; set; }
```

これらの変更をデータベースにプッシュするには、 **Update データベース**を実行するだけです。 ただし、null 非許容のブログを追加**しています。評価**列には、テーブルに既存のデータがある場合は、新しい列のデータ型の CLR の既定値が割り当てられます (評価は整数であり、 **0**になります)。 ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。
ここでは、[移行の追加] コマンドを使用して、コードベースの移行にこの変更を書き込んで、編集できるようにしましょう。 **[移行の追加]** コマンドを使用すると、これらの移行に名前を付けることができます。私たちの**AddBlogRating**を呼び出すだけです。

-   パッケージマネージャーコンソールで、 **[AddBlogRating の追加と移行]** コマンドを実行します。
-   **移行**フォルダーに、新しい**AddBlogRating**移行が追加されました。 移行ファイル名は、順序付けを支援するタイムスタンプで事前に固定されています。 生成されたコードを編集して、ブログの既定値3を指定してみましょう (下のコードの10行目)

*移行には、いくつかのメタデータをキャプチャする分離コードファイルもあります。このメタデータにより、Code First Migrations は、このコードベースの移行の前に実行した自動移行をレプリケートできます。これは、別の開発者が移行を実行する場合、またはアプリケーションをデプロイする時期がある場合に重要です。*

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

-   パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。

## <a name="back-to-automatic-migrations"></a>自動移行に戻る

自動移行に切り替えて、簡単に変更できるようになりました。 Code First Migrations は、コードベースの移行のたびに分離コードファイルに格納されているメタデータに基づいて、自動およびコードベースの移行を正しい順序で実行します。

-   ここでは、モデルに "Post. 抽象" プロパティを追加します。

``` csharp
    public string Abstract { get; set; }
```

これで、**更新データベース**を使用して、自動移行を使用してこの変更をデータベースにプッシュ Code First Migrations を取得できるようになりました。

-   パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。

## <a name="summary"></a>まとめ

このチュートリアルでは、自動移行を使用してモデルの変更をデータベースにプッシュする方法を説明しました。 さらに制御が必要な場合に、自動移行の間にコードベースの移行をスキャフォールディングして実行する方法についても説明しました。
