---
title: 検証-EF6
description: Entity Framework 6 での検証
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 224cdd8b033b0ea534efb547fd4d39ac922a6faa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064407"
---
# <a name="data-validation"></a>データ検証
> [!NOTE]
> **Ef 4.1 以降のみ** -このページで説明した機能、api などは、Entity Framework 4.1 で導入されました。 以前のバージョンを使用している場合、情報の一部またはすべてが適用されない

このページの内容は、最初にジュリー Lerman () によって作成された記事から適用され [https://thedatafarm.com](https://thedatafarm.com) ます。

Entity Framework には、クライアント側の検証のためにユーザーインターフェイスに渡すことができる、またはサーバー側の検証に使用する、さまざまな検証機能が用意されています。 Code first を使用する場合は、注釈または fluent API 構成を使用して検証を指定できます。 追加の検証 (さらに複雑) は、コードで指定できます。また、モデルが code first、model first、database first のいずれから susan されているかにかかわらず機能します。

## <a name="the-model"></a>Model

ここでは、簡単なクラスのペア (ブログと Post) で検証について説明します。

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }
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

Code First は、 `System.ComponentModel.DataAnnotations` コードの最初のクラスを構成する1つの手段として、アセンブリの注釈を使用します。 これらの注釈の中には、、などの規則を提供する注釈があり `Required` `MaxLength` `MinLength` ます。 多くの .NET クライアントアプリケーションでは、これらの注釈も認識しています。たとえば、ASP.NET MVC です。 これらの注釈を使用して、クライアント側とサーバー側の両方の検証を実現できます。 たとえば、"ブログのタイトル" プロパティを強制的に必須のプロパティにすることができます。

``` csharp
[Required]
public string Title { get; set; }
```

アプリケーションで追加のコードやマークアップの変更を加えなくても、既存の MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージを動的に構築しながら、クライアント側の検証を実行します。

![図 1](~/ef6/media/figure01.png)

この Create view のポストバックメソッドでは、新しいブログをデータベースに保存するために Entity Framework が使用されますが、アプリケーションがそのコードに到達する前に MVC のクライアント側の検証がトリガーされます。

ただし、クライアント側の検証は箇条書きではありません。 ユーザーはブラウザーの機能に影響を与える可能性がありますが、さらに悪いことに、ハッカーは UI 検証を回避するために trickery を使用することがあります。 ただし、Entity Framework も `Required` 注釈を認識して検証します。

これをテストする簡単な方法は、MVC のクライアント側の検証機能を無効にすることです。 これは、MVC アプリケーションの web.config ファイルで行うことができます。 AppSettings セクションには、ClientValidationEnabled のキーがあります。 このキーを false に設定すると、UI が検証を実行できなくなります。

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

クライアント側の検証が無効になっている場合でも、アプリケーションで同じ応答が返されます。 "タイトルフィールドが必要です" というエラーメッセージが以前と同様に表示されます。 ただし、これを除き、サーバー側の検証の結果になります。 Entity Framework は、 `Required` (面倒でも、データベースに送信するコマンドを構築する前に) 注釈の検証を実行 `INSERT` し、メッセージを表示する MVC にエラーを返します。

## <a name="fluent-api"></a>Fluent API

注釈ではなく code first の fluent API を使用して、同じクライアント側 & サーバー側検証を取得できます。 これを使用するのではなく `Required` 、MaxLength 検証を使用します。

Fluent API 構成は code first で適用され、クラスからモデルを構築します。 DbContext クラスの OnModelCreating メソッドをオーバーライドすることによって、構成を挿入できます。 次に示すのは、ブログ Gername プロパティが10文字以内であることを指定する構成です。

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

Fluent API の構成に基づいてスローされた検証エラーは自動的に UI には表示されませんが、コードでキャプチャし、それに応じて応答することができます。

次に、アプリケーションの blog コントローラークラスにおける例外処理エラーコードを示します。これにより、Entity Framework が、ブログのブログに、最大10文字を超える blog Gername を使用して保存しようとしたときに、その検証エラーがキャプチャされます。

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
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

検証は自動的にビューに戻されないため、を使用する追加のコードが `ModelState.AddModelError` 使用されています。 これにより、エラーの詳細によって、htmlhelper を使用してエラーが表示されるビューに対して、エラーの詳細が表示され `ValidationMessageFor` ます。

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` はに存在するインターフェイスです `System.ComponentModel.DataAnnotations` 。 Entity Framework API には含まれていませんが、Entity Framework クラスでサーバー側の検証に使用することもできます。 `IValidatableObject``Validate`Entity Framework が SaveChanges 中に呼び出すメソッドを提供します。または、クラスを検証するたびにを呼び出すことができます。

やなどの `Required` 構成 `MaxLength` では、1つのフィールドに対して検証を実行します。 メソッドでは、 `Validate` 2 つのフィールドの比較など、さらに複雑なロジックを使用できます。

次の例では、 `Blog` クラスはを実装するように拡張されており、 `IValidatableObject` とが一致しないという規則を提供してい `Title` `BloggerName` ます。

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
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

`ValidationResult`コンストラクターは、 `string` エラーメッセージと、 `string` 検証に関連付けられているメンバー名を表すの配列を表すを受け取ります。 この検証 `Title` ではとの両方がチェックされるため `BloggerName` 、両方のプロパティ名が返されます。

Fluent API によって提供される検証とは異なり、この検証結果はビューによって認識され、前にエラーを追加するために使用した例外ハンドラー `ModelState` は不要です。 では両方のプロパティ名を設定しているため、 `ValidationResult` これらのプロパティの両方について、MVC HtmlHelpers によってエラーメッセージが表示されます。

![図 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext. ValidateEntity

`DbContext` には、というオーバーライド可能なメソッドがあり `ValidateEntity` ます。 を呼び出すと `SaveChanges` 、状態がではないキャッシュ内の各エンティティに対して、Entity Framework がこのメソッドを呼び出し `Unchanged` ます。 ここで検証ロジックを直接配置することも、このメソッドを使用して前のセクションで追加したメソッドなどを呼び出すこともでき `Blog.Validate` ます。

次に、新しいを検証して `ValidateEntity` `Post` 、投稿タイトルが既に使用されていないことを確認するオーバーライドの例を示します。 まず、エンティティが post であるかどうか、およびその状態が追加されているかどうかを確認します。 その場合は、データベースを調べて、同じタイトルの投稿が既に存在するかどうかを確認します。 既存の投稿が既に存在する場合は、新しい `DbEntityValidationResult` が作成されます。

`DbEntityValidationResult``DbEntityEntry` `ICollection<DbValidationErrors>` 1 つのエンティティに対してとを格納します。 このメソッドの開始時に、 `DbEntityValidationResult` がインスタンス化され、検出されたすべてのエラーがコレクションに追加され `ValidationErrors` ます。

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
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

## <a name="explicitly-triggering-validation"></a>明示的な検証のトリガー

を呼び出すと `SaveChanges` 、この記事で説明されているすべての検証がトリガーされます。 ただし、に依存する必要はありません `SaveChanges` 。 アプリケーション内の他の場所でも検証することをお勧めします。

`DbContext.GetValidationErrors` では、すべての検証がトリガーされます。これは、注釈または Fluent API によって定義された検証、で作成された検証 `IValidatableObject` (たとえば `Blog.Validate` )、およびメソッドで実行された検証 `DbContext.ValidateEntity` です。

次のコードは `GetValidationErrors` 、の現在のインスタンスでを呼び出し `DbContext` ます。 `ValidationErrors` は、エンティティ型によってにグループ化され `DbEntityValidationResult` ます。 このコードは、最初に `DbEntityValidationResult` メソッドによって返されたを通じて、次に内部で反復処理し `DbValidationError` ます。

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a>検証を使用する場合のその他の考慮事項

Entity Framework 検証を使用する場合は、次の点を考慮する必要があります。

- 遅延読み込みは検証中に無効になります
- EF は、マップされていないプロパティ (データベース内の列にマップされていないプロパティ) のデータ注釈を検証します。
- 検証は、の間に変更が検出された後に実行され `SaveChanges` ます。 検証中に変更を行う場合は、変更トラッカーに通知する必要があります。
- `DbUnexpectedValidationException` 検証中にエラーが発生した場合にスローされます
- クラスにデータ注釈がない場合や、EF デザイナーを使用してモデルを作成した場合でも、モデルに含まれている Entity Framework ファセット (最大長、必須など) によって検証が行われます。
- 優先順位の規則:
  - Fluent API 呼び出しは、対応するデータ注釈をオーバーライドします
- 実行順序:
  - プロパティの検証は、型の検証の前に行われます
  - 型の検証は、プロパティの検証が成功した場合にのみ発生します
- プロパティが複雑な場合、その検証にも次の内容が含まれます。
  - 複合型プロパティのプロパティレベルの検証
  - 複合型の検証を含む複合型の型レベルの検証 `IValidatableObject`

## <a name="summary"></a>まとめ

Entity Framework の検証 API は、MVC でのクライアント側の検証に非常に適していますが、クライアント側の検証に依存する必要はありません。 Entity Framework は、code first Fluent API で適用した DataAnnotations または構成のサーバー側での検証を処理します。

また、インターフェイスを使用するか、メソッドをタップするかにかかわらず、動作をカスタマイズするための拡張ポイントがいくつか表示されて `IValidatableObject` `DbContext.ValidateEntity` います。 `DbContext`また、Code First、Model First または Database First のワークフローを使用して概念モデルを記述するかどうかによって、これらの検証の2つの方法がで利用できます。
