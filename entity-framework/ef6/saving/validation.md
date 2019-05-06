---
title: EF6 の検証
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 98d7bd08d841ee400afb62e1079f1a965f65e139
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316648"
---
# <a name="data-validation"></a>データの検証
> [!NOTE]
> **EF4.1 以降のみ**-機能、Api、Entity Framework 4.1 で導入されたなどのこのページで説明します。 一部またはすべての情報は適用されませんが、以前のバージョンを使用している場合

このページの内容を Julie Lerman によって書き込まれた最初の記事からの抜粋です ([http://thedatafarm.com](http://thedatafarm.com))。

Entity Framework では、さまざまなクライアント側検証用のユーザー インターフェイスをフィードできますまたはサーバー側の検証に使用される検証機能を提供します。 最初にコードを使用している場合は、注釈または fluent API 構成を使用して検証を指定できます。 追加の検証、およびより複雑なコードで指定することができ、、最初に、メイン コードからモデルかどうかを最初にモデルまたはデータベースの最初。

## <a name="the-model"></a>モデル

クラスの単純なペアに検証を紹介します。ブログの投稿.

``` csharp
    public class Blog
      {
          public int Id { get; set; }
          public string Title { get; set; }
          public string BloggerName { get; set; }
          public DateTime DateCreated { get; set; }
          public virtual ICollection<Post> Posts { get; set; }
          }
      }

      public class Post
      {
          public int Id { get; set; }
          public string Title { get; set; }
          public DateTime DateCreated { get; set; }
          public string Content { get; set; }
          public int BlogId { get; set; }
          public ICollection<Comment> Comments { get; set; }
      }
```

## <a name="data-annotations"></a>データの注釈

まず、コードは、コードの最初のクラスの設定の 1 つの手段として System.ComponentModel.DataAnnotations アセンブリからの注釈を使用します。 これらの注釈の間で必要な場合は、MaxLength、MinLength などのルールが提供されるは。 多数の .NET クライアント アプリケーションでは、これらの注釈では、たとえば、ASP.NET MVC も認識します。 両方クライアント側とサーバー側の検証では、これらの注釈を実現できます。 たとえば、ブログのタイトル プロパティに必要なプロパティを強制できます。

``` csharp
    [Required]
    public string Title { get; set; }
```

コードを追加またはアプリケーションのマークアップを変更なしに、既存の MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージの構築も動的に、クライアント側の検証を実行します。

![図 1](~/ef6/media/figure01.png)

投稿内 back のこのビューを作成するメソッド、Entity Framework を使用して、データベースに新しいブログを保存するが、アプリケーションがそのコードに到達する前に、MVC のクライアント側の検証がトリガーされます。

クライアント側の検証は堅牢なただしします。 ユーザーが自分のブラウザーの機能に影響を与えるかさらに悪いことまだ、ハッカー可能性がありますを使用してちょっとしたテクニックがいくつか UI 検証を回避します。 Entity Framework また必要な注釈を認識し、それを検証します。

これをテストする簡単な方法では、MVC のクライアント側の検証機能を無効にします。 MVC アプリケーションの web.config ファイルで、これを行うことができます。 AppSettings セクションには、ClientValidationEnabled のキーがあります。 このキーを false に設定すると、UI が検証を実行することはできなくなります。

``` xml
    <appSettings>
        <add key="ClientValidationEnabled"value="false"/>
        ...
    </appSettings>
```

クライアント側検証を無効にしてを使用しても、アプリケーションで同じ応答が表示されます。 エラー メッセージ「タイトル フィールドが必要」として表示されます。 これ以外のサーバー側の検証の結果があります。 Entity Framework は、に対して検証を必要な注釈 (がビルドされ、データベースに送信する INSERT コマンドにも面倒) する前に mvc メッセージが表示されるエラーを返します。

## <a name="fluent-api"></a>Fluent API

コードを使用する側とサーバーは、同じクライアントを取得する注釈ではなく最初の fluent API 側の検証。 必要な使用をご紹介します MaxLength 検証を使用してこのなく。

コードはまず、クラスからモデルを構築は、Fluent API 構成が適用されます。 DbContext クラスの OnModelCreating メソッドをオーバーライドすることで、構成を挿入できます。 BloggerName プロパティを 10 文字以内にできることを指定する構成を次に示します。

``` csharp
    public class BlogContext : DbContext
      {
          public DbSet<Blog> Blogs { get; set; }
          public DbSet<Post> Posts { get; set; }
          public DbSet<Comment> Comments { get; set; }

          protected override void OnModelCreating(DbModelBuilder modelBuilder)
          {
              modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
          }
        }
```

検証エラーがスローされた Fluent API 構成に基づいたは自動的に reach UI ことができますキャプチャしません、コードと次の応答でそれに応じて。

ここでの例外が処理エラー コードは、Entity Framework が 10 文字の最大値を超える BloggerName でブログを保存しようとしたときに、その検証エラーをキャプチャするアプリケーションの BlogController クラスにいくつか。

``` csharp
    [HttpPost]
    public ActionResult Edit(int id, Blog blog)
    {
        try
        {
            db.Entry(blog).State = EntityState.Modified;
            db.SaveChanges();
            return RedirectToAction("Index");
        }
        catch(DbEntityValidationException ex)
        {
            var error = ex.EntityValidationErrors.First().ValidationErrors.First();
            this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
            return View();
        }
    }
```

検証は、ModelState.AddModelError を使用する追加コードが使用されている理由は、このビューに再び渡さ自動的にしません。 これにより、ビューを使用して、作成された ValidationMessageFor Htmlhelper エラーを表示するにように、エラーの詳細。

``` csharp
    @Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

IValidatableObject は、System.ComponentModel.DataAnnotations に存在するインターフェイスです。 Entity Framework API の一部ではありませんが、利用でき、サーバー側の検証の Entity Framework のクラスで。 Entity Framework を呼び出すか、SaveChanges 中に検証メソッド呼び出すことができます自分でいつでも、クラスを検証する IValidatableObject を提供します。

など、必要な構成と MaxLength は、1 つのフィールド検証を実行します。 検証メソッドには 2 つのフィールドを比較するより複雑なロジックができます。

次の例では、IValidatableObject を実装し、タイトルと BloggerName ことはできませんと一致する規則を指定するブログ クラスが拡張されました。

``` csharp
    public class Blog : IValidatableObject
     {
         public int Id { get; set; }
         [Required]
         public string Title { get; set; }
         public string BloggerName { get; set; }
         public DateTime DateCreated { get; set; }
         public virtual ICollection<Post> Posts { get; set; }

         public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
         {
             if (Title == BloggerName)
             {
                 yield return new ValidationResult
                  ("Blog Title cannot match Blogger Name", new[] { "Title", “BloggerName” });
             }
         }
     }
```

ValidationResult コンストラクターでは、エラー メッセージと、検証に関連付けられているメンバー名を表す文字列の配列を表す文字列を受け取ります。 この検証では、タイトルと、BloggerName の両方をチェック、ため両方のプロパティ名が返されます。

Fluent API によって提供される検証とは異なり、ビューでこの検証の結果が認識され、ModelState にエラーを追加する前に使用した例外ハンドラーが必要ではありません。 ValidationResult で両方のプロパティ名を設定するため、これらのプロパティの両方のエラー メッセージが表示 MVC HtmlHelpers します。

![図 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext.ValidateEntity

DbContext では、ValidateEntity と呼ばれる、オーバーライド可能なメソッドがあります。 SaveChanges を呼び出すときに Entity Framework は、キャッシュの状態が変更されていない各エンティティに対してこのメソッドを呼び出します。 、たとえば、前のセクションで追加 Blog.Validate メソッドを呼び出すには、このメソッドで、ここでも使用を直接検証ロジックを格納できます。

新しい投稿への投稿のタイトルが既に使用されていないことを確認することを検証する ValidateEntity 上書きの例を次に示します。 エンティティは、投稿、かどうかと、状態が追加されたことを確認します。 場合は、し、検索、データベース内に同じタイトルの投稿があるかどうかを参照してください。 既に既存の投稿がある、新しい DbEntityValidationResult が作成されます。

DbEntityValidationResult には、対象の DbEntityEntry と単一のエンティティの ICollection の DbValidationErrors が格納されています。 このメソッドの先頭を DbEntityValidationResult がインスタンス化され、その ValidationErrors コレクションに検出されたエラーを追加する、します。

``` csharp
    protected override DbEntityValidationResult ValidateEntity (
        System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
        IDictionary\<object, object> items)
    {
        var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());
        if (entityEntry.Entity is Post && entityEntry.State == EntityState.Added)
        {
            Post post = entityEntry.Entity as Post;
            //check for uniqueness of post title
            if (Posts.Where(p => p.Title == post.Title).Count() > 0)
            {
                result.ValidationErrors.Add(
                        new System.Data.Entity.Validation.DbValidationError("Title",
                        "Post title must be unique."));
            }
        }

        if (result.ValidationErrors.Count > 0)
        {
            return result;
        }
        else
        {
         return base.ValidateEntity(entityEntry, items);
        }
    }
```

## <a name="explicitly-triggering-validation"></a>明示的に検証をトリガーします。

SaveChanges への呼び出しは、この記事で説明する検証のすべてをトリガーします。 ただし、SaveChanges に依存する必要はありません。 アプリケーション内の他の場所を検証することもできます。

DbContext.GetValidationErrors はすべての検証、注釈または Fluent API で定義されている、IValidatableObject (たとえば、Blog.Validate) で作成された、検証、DbContext.ValidateEntity で実行される検証をトリガーします。メソッド。

次のコードでは、DbContext の現在のインスタンスで GetValidationErrors を呼び出します。 ValidationErrors は DbValidationResults にエンティティの種類でグループ化されます。 メソッドによって返される DbValidationResults とし、内の各 ValidationError を介して、コードが最初に反復処理します。

``` csharp
    foreach (var validationResults in db.GetValidationErrors())
        {
            foreach (var error in validationResults.ValidationErrors)
            {
                Debug.WriteLine(
                                  "Entity Property: {0}, Error {1}",
                                  error.PropertyName,
                                  error.ErrorMessage);
            }
        }
```

## <a name="other-considerations-when-using-validation"></a>検証を使用する場合に、その他の考慮事項

Entity Framework の検証を使用する際に考慮するその他のいくつかの点を次に示します。

-   検証中には、遅延読み込みが無効です。
-   EF では、データ注釈で、マップされていないプロパティ (データベース内の列にマップされていないプロパティ) を検証します。
-   検証は、SaveChanges 中に変更が検出された後に実行されます。 検証中に変更を加えた場合は、変更トラッカーに通知する責任を勧めします。
-   検証中にエラーが発生した場合、DbUnexpectedValidationException がスローされます。
-   (必要な最大の長さなど)、モデルに Entity Framework を含むファセットには、クラスのデータの注釈がないや、モデルの作成に EF Designer を使用した場合でも、検証になります。
-   優先順位の規則:
    -   Fluent API の呼び出しが対応するデータ注釈を上書き
-   実行の順序:
    -   プロパティの検証は、型の検証の前に発生します
    -   型の検証は、プロパティの検証が成功した場合にのみ発生します
-   プロパティが複雑な場合、検証も含まれます。
    -   複合型のプロパティに対するプロパティ レベルの検証
    -   複合型 IValidatableObject 評価を含む、複合型で型レベルの検証

## <a name="summary"></a>まとめ

MVC でのクライアント側の検証と Entity Framework での検証 API が非常にうまく再生しますが、クライアント側の検証に依存する必要はありません。 Entity Framework は、DataAnnotations または code first Fluent API で適用した構成の検証をサーバー側での考慮されます。

IValidatableObject インターフェイスを使用してまたは DbContet.ValidateEntity メソッドをタップするかどうか、動作をカスタマイズするための機能拡張ポイント数も確認しました。 Code First、Model First または Database First ワークフローを使用して、概念モデルを記述するかどうかは、これらの検証の最後の 2 つの方法は、DbContext から入手できます。
