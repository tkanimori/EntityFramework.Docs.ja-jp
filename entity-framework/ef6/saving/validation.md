---
title: 検証-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 2c5e6f1b3f60862124bafcac42e8859a7591f8e6
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812151"
---
# <a name="data-validation"></a>データの検証
> [!NOTE]
> **Ef 4.1 以降のみ**-このページで説明した機能、api などは、Entity Framework 4.1 で導入されました。 以前のバージョンを使用している場合、情報の一部またはすべてが適用されない

このページの内容は、最初にジュリー Lerman ([https://thedatafarm.com](https://thedatafarm.com)) によって作成された記事から適用されます。

Entity Framework には、クライアント側の検証のためにユーザーインターフェイスに渡すことができる、またはサーバー側の検証に使用する、さまざまな検証機能が用意されています。 Code first を使用する場合は、注釈または fluent API 構成を使用して検証を指定できます。 追加の検証 (さらに複雑) は、コードで指定できます。また、モデルが code first、model first、database first のいずれから susan されているかにかかわらず機能します。

## <a name="the-model"></a>モデル

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

Code First は、code First クラスを構成する1つの手段として、`System.ComponentModel.DataAnnotations` アセンブリの注釈を使用します。 これらの注釈の中には、`Required`、`MaxLength`、`MinLength`などの規則を提供する注釈があります。 多くの .NET クライアントアプリケーションでは、これらの注釈も認識しています。たとえば、ASP.NET MVC です。 これらの注釈を使用して、クライアント側とサーバー側の両方の検証を実現できます。 たとえば、"ブログのタイトル" プロパティを強制的に必須のプロパティにすることができます。

``` csharp
[Required]
public string Title { get; set; }
```

アプリケーションで追加のコードやマークアップの変更を加えなくても、既存の MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージを動的に構築しながら、クライアント側の検証を実行します。

![図1](~/ef6/media/figure01.png)

この Create view のポストバックメソッドでは、新しいブログをデータベースに保存するために Entity Framework が使用されますが、アプリケーションがそのコードに到達する前に MVC のクライアント側の検証がトリガーされます。

ただし、クライアント側の検証は箇条書きではありません。 ユーザーはブラウザーの機能に影響を与える可能性がありますが、さらに悪いことに、ハッカーは UI 検証を回避するために trickery を使用することがあります。 ただし、Entity Framework は `Required` 注釈も認識し、検証します。

これをテストする簡単な方法は、MVC のクライアント側の検証機能を無効にすることです。 これは、MVC アプリケーションの web.config ファイルで行うことができます。 AppSettings セクションには、ClientValidationEnabled のキーがあります。 このキーを false に設定すると、UI が検証を実行できなくなります。

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

クライアント側の検証が無効になっている場合でも、アプリケーションで同じ応答が返されます。 "タイトルフィールドが必要です" というエラーメッセージが以前と同様に表示されます。 ただし、これを除き、サーバー側の検証の結果になります。 Entity Framework は `Required` 注釈に対して検証を実行し (データベースに送信する `INSERT` コマンドを作成する前に面倒)、メッセージを表示する MVC にエラーを返します。

## <a name="fluent-api"></a>Fluent API

注釈ではなく code first の fluent API を使用して、同じクライアント側 & サーバー側検証を取得できます。 `Required`を使用するのではなく、MaxLength 検証を使用してこれを示します。

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

検証は、ビューに自動的に戻されるわけではないため、`ModelState.AddModelError` を使用する追加のコードが使用されています。 これにより、エラーの詳細によってビューが表示され、`ValidationMessageFor` Htmlhelper を使用してエラーが表示されるようになります。

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` は `System.ComponentModel.DataAnnotations`内に存在するインターフェイスです。 Entity Framework API には含まれていませんが、Entity Framework クラスでサーバー側の検証に使用することもできます。 `IValidatableObject` には、SaveChanges 中に Entity Framework が呼び出す `Validate` メソッドが用意されています。また、クラスを検証するときは、いつでも呼び出すことができます。

`Required` や `MaxLength` などの構成では、1つのフィールドに対して検証が実行されます。 `Validate` メソッドでは、2つのフィールドの比較など、さらに複雑なロジックを使用できます。

次の例では、`IValidatableObject` を実装するように `Blog` クラスが拡張されており、`Title` と `BloggerName` が一致しないという規則を提供しています。

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

`ValidationResult` コンストラクターは、エラーメッセージを表す `string` と、検証に関連付けられているメンバー名を表す `string`の配列を受け取ります。 この検証では `Title` と `BloggerName`の両方がチェックされるため、両方のプロパティ名が返されます。

Fluent API によって提供される検証とは異なり、この検証結果はビューによって認識されます。また、前にエラーを追加するために使用した例外ハンドラー `ModelState` は不要です。 `ValidationResult`で両方のプロパティ名を設定したため、MVC HtmlHelpers には、両方のプロパティのエラーメッセージが表示されます。

![図2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext. ValidateEntity

`DbContext` には、`ValidateEntity`と呼ばれるオーバーライド可能なメソッドがあります。 `SaveChanges`を呼び出すと、Entity Framework は、状態が `Unchanged`ではないキャッシュ内の各エンティティに対してこのメソッドを呼び出します。 ここに検証ロジックを直接配置することも、このメソッドを使用して前のセクションで追加した `Blog.Validate` メソッドを呼び出すこともできます。

新しい `Post`を検証して、投稿タイトルが既に使用されていないことを確認する `ValidateEntity` オーバーライドの例を次に示します。 まず、エンティティが post であるかどうか、およびその状態が追加されているかどうかを確認します。 その場合は、データベースを調べて、同じタイトルの投稿が既に存在するかどうかを確認します。 既存の投稿が既に存在する場合は、新しい `DbEntityValidationResult` が作成されます。

`DbEntityValidationResult` は、1つのエンティティの `DbEntityEntry` と `ICollection<DbValidationErrors>` を格納します。 このメソッドの開始時に、`DbEntityValidationResult` がインスタンス化され、検出されたエラーが `ValidationErrors` コレクションに追加されます。

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

`SaveChanges` を呼び出すと、この記事で説明されているすべての検証がトリガーされます。 ただし、`SaveChanges`に依存する必要はありません。 アプリケーション内の他の場所でも検証することをお勧めします。

`DbContext.GetValidationErrors` によって、すべての検証がトリガーされます。これには、注釈や Fluent API によって定義された検証、`IValidatableObject` で作成された検証 (`Blog.Validate`など)、および `DbContext.ValidateEntity` メソッドで実行された検証が含まれます。

次のコードは、`DbContext`の現在のインスタンスで `GetValidationErrors` を呼び出します。 `ValidationErrors` は、エンティティ型で `DbEntityValidationResult`にグループ化されます。 このコードは、最初にメソッドによって返された `DbEntityValidationResult`を使用し、次に内部の各 `DbValidationError` を反復処理します。

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
- 検証は、`SaveChanges`中に変更が検出された後に実行されます。 検証中に変更を行う場合は、変更トラッカーに通知する必要があります。
- 検証中にエラーが発生すると `DbUnexpectedValidationException` がスローされる
- クラスにデータ注釈がない場合や、EF デザイナーを使用してモデルを作成した場合でも、モデルに含まれている Entity Framework ファセット (最大長、必須など) によって検証が行われます。
- 優先順位の規則:
  - Fluent API 呼び出しは、対応するデータ注釈をオーバーライドします
- 実行順序:
  - プロパティの検証は、型の検証の前に行われます
  - 型の検証は、プロパティの検証が成功した場合にのみ発生します
- プロパティが複雑な場合、その検証にも次の内容が含まれます。
  - 複合型プロパティのプロパティレベルの検証
  - 複合型の `IValidatableObject` 検証を含む、複合型の型レベルの検証

## <a name="summary"></a>まとめ

Entity Framework の検証 API は、MVC でのクライアント側の検証に非常に適していますが、クライアント側の検証に依存する必要はありません。 Entity Framework は、code first Fluent API で適用した DataAnnotations または構成のサーバー側での検証を処理します。

また、`IValidatableObject` インターフェイスを使用するか、`DbContext.ValidateEntity` 方法をタップするかにかかわらず、動作をカスタマイズするための拡張ポイントがいくつか表示されています。 また、Code First、Model First または Database First のワークフローを使用して概念モデルを記述するかどうかにかかわらず、`DbContext`を通じて、これらの最後の2つの方法を利用できます。
