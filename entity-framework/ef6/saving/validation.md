---
title: 検証-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 457af0c32f0fe4804dbfe6e348664efb1af517c9
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565376"
---
# <a name="data-validation"></a><span data-ttu-id="efa66-102">データの検証</span><span class="sxs-lookup"><span data-stu-id="efa66-102">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="efa66-103">**Ef 4.1 以降のみ**-このページで説明した機能、api などは、Entity Framework 4.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="efa66-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="efa66-104">以前のバージョンを使用している場合、情報の一部またはすべてが適用されない</span><span class="sxs-lookup"><span data-stu-id="efa66-104">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="efa66-105">このページの内容は、最初にジュリー Lerman ([http://thedatafarm.com](http://thedatafarm.com)) によって作成された記事から適用されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-105">The content on this page is adapted from an article originally written by Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)).</span></span>

<span data-ttu-id="efa66-106">Entity Framework には、クライアント側の検証のためにユーザーインターフェイスに渡すことができる、またはサーバー側の検証に使用する、さまざまな検証機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="efa66-106">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="efa66-107">Code first を使用する場合は、注釈または fluent API 構成を使用して検証を指定できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-107">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="efa66-108">追加の検証 (さらに複雑) は、コードで指定できます。また、モデルが code first、model first、database first のいずれから susan されているかにかかわらず機能します。</span><span class="sxs-lookup"><span data-stu-id="efa66-108">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="efa66-109">モデル</span><span class="sxs-lookup"><span data-stu-id="efa66-109">The model</span></span>

<span data-ttu-id="efa66-110">ここでは、単純なクラスのペアによる検証について説明します。ブログと投稿。</span><span class="sxs-lookup"><span data-stu-id="efa66-110">I'll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="efa66-111">データの注釈</span><span class="sxs-lookup"><span data-stu-id="efa66-111">Data Annotations</span></span>

<span data-ttu-id="efa66-112">Code First は、コードの`System.ComponentModel.DataAnnotations`最初のクラスを構成する1つの手段として、アセンブリの注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="efa66-112">Code First uses annotations from the `System.ComponentModel.DataAnnotations` assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="efa66-113">これらの注釈の中には、 `Required` `MinLength`、 `MaxLength`などの規則を提供する注釈があります。</span><span class="sxs-lookup"><span data-stu-id="efa66-113">Among these annotations are those which provide rules such as the `Required`, `MaxLength` and `MinLength`.</span></span> <span data-ttu-id="efa66-114">多くの .NET クライアントアプリケーションでは、これらの注釈も認識しています。たとえば、ASP.NET MVC です。</span><span class="sxs-lookup"><span data-stu-id="efa66-114">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="efa66-115">これらの注釈を使用して、クライアント側とサーバー側の両方の検証を実現できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-115">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="efa66-116">たとえば、"ブログのタイトル" プロパティを強制的に必須のプロパティにすることができます。</span><span class="sxs-lookup"><span data-stu-id="efa66-116">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
[Required]
public string Title { get; set; }
```

<span data-ttu-id="efa66-117">アプリケーションで追加のコードやマークアップの変更を加えなくても、既存の MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージを動的に構築しながら、クライアント側の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="efa66-117">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![図 1](~/ef6/media/figure01.png)

<span data-ttu-id="efa66-119">この Create view のポストバックメソッドでは、新しいブログをデータベースに保存するために Entity Framework が使用されますが、アプリケーションがそのコードに到達する前に MVC のクライアント側の検証がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="efa66-119">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC's client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="efa66-120">ただし、クライアント側の検証は箇条書きではありません。</span><span class="sxs-lookup"><span data-stu-id="efa66-120">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="efa66-121">ユーザーはブラウザーの機能に影響を与える可能性がありますが、さらに悪いことに、ハッカーは UI 検証を回避するために trickery を使用することがあります。</span><span class="sxs-lookup"><span data-stu-id="efa66-121">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="efa66-122">ただし、 `Required` Entity Framework も注釈を認識して検証します。</span><span class="sxs-lookup"><span data-stu-id="efa66-122">But Entity Framework will also recognize the `Required` annotation and validate it.</span></span>

<span data-ttu-id="efa66-123">これをテストする簡単な方法は、MVC のクライアント側の検証機能を無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="efa66-123">A simple way to test this is to disable MVC's client-side validation feature.</span></span> <span data-ttu-id="efa66-124">これは、MVC アプリケーションの web.config ファイルで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="efa66-124">You can do this in the MVC application's web.config file.</span></span> <span data-ttu-id="efa66-125">AppSettings セクションには、ClientValidationEnabled のキーがあります。</span><span class="sxs-lookup"><span data-stu-id="efa66-125">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="efa66-126">このキーを false に設定すると、UI が検証を実行できなくなります。</span><span class="sxs-lookup"><span data-stu-id="efa66-126">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

<span data-ttu-id="efa66-127">クライアント側の検証が無効になっている場合でも、アプリケーションで同じ応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-127">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="efa66-128">"タイトルフィールドが必要です" というエラーメッセージが以前と同様に表示されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-128">The error message "The Title field is required" will be displayed as before.</span></span> <span data-ttu-id="efa66-129">ただし、これを除き、サーバー側の検証の結果になります。</span><span class="sxs-lookup"><span data-stu-id="efa66-129">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="efa66-130">Entity Framework は、(面倒でも`Required` 、データベースに送信する`INSERT`コマンドを構築する前に) 注釈の検証を実行し、メッセージを表示する MVC にエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="efa66-130">Entity Framework will perform the validation on the `Required` annotation (before it even bothers to build an `INSERT` command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="efa66-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="efa66-131">Fluent API</span></span>

<span data-ttu-id="efa66-132">注釈ではなく code first の fluent API を使用して、同じクライアント側 & サーバー側検証を取得できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-132">You can use code first's fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="efa66-133">これを使用`Required`するのではなく、MaxLength 検証を使用します。</span><span class="sxs-lookup"><span data-stu-id="efa66-133">Rather than use `Required`, I'll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="efa66-134">Fluent API 構成は code first で適用され、クラスからモデルを構築します。</span><span class="sxs-lookup"><span data-stu-id="efa66-134">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="efa66-135">DbContext クラスの OnModelCreating メソッドをオーバーライドすることによって、構成を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-135">You can inject the configurations by overriding the DbContext class' OnModelCreating  method.</span></span> <span data-ttu-id="efa66-136">次に示すのは、ブログ Gername プロパティが10文字以内であることを指定する構成です。</span><span class="sxs-lookup"><span data-stu-id="efa66-136">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

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

<span data-ttu-id="efa66-137">Fluent API の構成に基づいてスローされた検証エラーは自動的に UI には表示されませんが、コードでキャプチャし、それに応じて応答することができます。</span><span class="sxs-lookup"><span data-stu-id="efa66-137">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="efa66-138">次に、アプリケーションの blog コントローラークラスにおける例外処理エラーコードを示します。これにより、Entity Framework が、ブログのブログに、最大10文字を超える blog Gername を使用して保存しようとしたときに、その検証エラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="efa66-138">Here's some exception handling error code in the application's BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

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

<span data-ttu-id="efa66-139">検証は自動的にビューに戻されないため、を使用`ModelState.AddModelError`する追加のコードが使用されています。</span><span class="sxs-lookup"><span data-stu-id="efa66-139">The validation doesn't automatically get passed back into the view which is why the additional code that uses `ModelState.AddModelError` is being used.</span></span> <span data-ttu-id="efa66-140">これにより、エラーの詳細によって、 `ValidationMessageFor` htmlhelper を使用してエラーが表示されるビューに対して、エラーの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-140">This ensures that the error details make it to the view which will then use the `ValidationMessageFor` Htmlhelper to display the error.</span></span>

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="efa66-141">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="efa66-141">IValidatableObject</span></span>

<span data-ttu-id="efa66-142">`IValidatableObject`はに`System.ComponentModel.DataAnnotations`存在するインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="efa66-142">`IValidatableObject` is an interface that lives in `System.ComponentModel.DataAnnotations`.</span></span> <span data-ttu-id="efa66-143">Entity Framework API には含まれていませんが、Entity Framework クラスでサーバー側の検証に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="efa66-143">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="efa66-144">`IValidatableObject`Entity Framework が`Validate` SaveChanges 中に呼び出すメソッドを提供します。または、クラスを検証するたびにを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="efa66-144">`IValidatableObject` provides a `Validate` method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="efa66-145">`Required` や`MaxLength`などの構成では、1つのフィールドに対して検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="efa66-145">Configurations such as `Required` and `MaxLength` perform validation on a single field.</span></span> <span data-ttu-id="efa66-146">`Validate`メソッドでは、2つのフィールドの比較など、さらに複雑なロジックを使用できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-146">In the `Validate` method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="efa66-147">次の例`Blog`では、クラスはを実装`IValidatableObject`するように拡張されており、 `Title`と`BloggerName`が一致しないという規則を提供しています。</span><span class="sxs-lookup"><span data-stu-id="efa66-147">In the following example, the `Blog` class has been extended to implement `IValidatableObject` and then provide a rule that the `Title` and `BloggerName` cannot match.</span></span>

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

<span data-ttu-id="efa66-148">コンストラクター `ValidationResult`は、エラー `string`メッセージと、検証に関連付けられ`string`ているメンバー名を表すの配列を表すを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="efa66-148">The `ValidationResult` constructor takes a `string` that represents the error message and an array of `string`s that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="efa66-149">この検証では`Title`との`BloggerName`両方がチェックされるため、両方のプロパティ名が返されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-149">Since this validation checks both the `Title` and the `BloggerName`, both property names are returned.</span></span>

<span data-ttu-id="efa66-150">Fluent API によって提供される検証とは異なり、この検証結果はビューによって認識され、前にエラー `ModelState`を追加するために使用した例外ハンドラーは不要です。</span><span class="sxs-lookup"><span data-stu-id="efa66-150">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into `ModelState` is unnecessary.</span></span> <span data-ttu-id="efa66-151">では`ValidationResult`両方のプロパティ名を設定しているため、これらのプロパティの両方について、MVC htmlhelpers によってエラーメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-151">Because I set both property names in the `ValidationResult`, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![図 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="efa66-153">DbContext. ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="efa66-153">DbContext.ValidateEntity</span></span>

<span data-ttu-id="efa66-154">`DbContext`には、という`ValidateEntity`オーバーライド可能なメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="efa66-154">`DbContext` has an overridable method called `ValidateEntity`.</span></span> <span data-ttu-id="efa66-155">を呼び出す`SaveChanges`と、状態がではない`Unchanged`キャッシュ内の各エンティティに対して、Entity Framework がこのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="efa66-155">When you call `SaveChanges`, Entity Framework will call this method for each entity in its cache whose state is not `Unchanged`.</span></span> <span data-ttu-id="efa66-156">ここで検証ロジックを直接配置することも、 `Blog.Validate`このメソッドを使用して前のセクションで追加したメソッドなどを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="efa66-156">You can put validation logic directly in here or even use this method to call, for example, the `Blog.Validate` method added in the previous section.</span></span>

<span data-ttu-id="efa66-157">次に、新しい`ValidateEntity` `Post`を検証して、投稿タイトルが既に使用されていないことを確認するオーバーライドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="efa66-157">Here's an example of a `ValidateEntity` override that validates new `Post`s to ensure that the post title hasn't been used already.</span></span> <span data-ttu-id="efa66-158">まず、エンティティが post であるかどうか、およびその状態が追加されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="efa66-158">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="efa66-159">その場合は、データベースを調べて、同じタイトルの投稿が既に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="efa66-159">If that's the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="efa66-160">既存の投稿が既に存在する場合は、 `DbEntityValidationResult`新しいが作成されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-160">If there is an existing post already, then a new `DbEntityValidationResult` is created.</span></span>

<span data-ttu-id="efa66-161">`DbEntityValidationResult`1つ`ICollection<DbValidationErrors>`のエンティティに対してとを格納します。`DbEntityEntry`</span><span class="sxs-lookup"><span data-stu-id="efa66-161">`DbEntityValidationResult` houses a `DbEntityEntry` and an `ICollection<DbValidationErrors>` for a single entity.</span></span> <span data-ttu-id="efa66-162">このメソッド`DbEntityValidationResult`の開始時に、がインスタンス化され、検出されたすべてのエラーが`ValidationErrors`コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-162">At the start of this method, a `DbEntityValidationResult` is instantiated and then any errors that are discovered are added into its `ValidationErrors` collection.</span></span>

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

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="efa66-163">明示的な検証のトリガー</span><span class="sxs-lookup"><span data-stu-id="efa66-163">Explicitly triggering validation</span></span>

<span data-ttu-id="efa66-164">を`SaveChanges`呼び出すと、この記事で説明されているすべての検証がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="efa66-164">A call to `SaveChanges` triggers all of the validations covered in this article.</span></span> <span data-ttu-id="efa66-165">ただし、に`SaveChanges`依存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="efa66-165">But you don't need to rely on `SaveChanges`.</span></span> <span data-ttu-id="efa66-166">アプリケーション内の他の場所でも検証することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="efa66-166">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="efa66-167">`DbContext.GetValidationErrors`では、すべての検証がトリガーされます。これは、注釈または Fluent API `IValidatableObject`によって定義`Blog.Validate`された検証、で作成さ`DbContext.ValidateEntity`れた検証 (たとえば)、およびメソッドで実行された検証です。</span><span class="sxs-lookup"><span data-stu-id="efa66-167">`DbContext.GetValidationErrors` will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in `IValidatableObject` (for example, `Blog.Validate`), and the validations performed in the `DbContext.ValidateEntity` method.</span></span>

<span data-ttu-id="efa66-168">次のコードは、 `GetValidationErrors` `DbContext`の現在のインスタンスでを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="efa66-168">The following code will call `GetValidationErrors` on the current instance of a `DbContext`.</span></span> <span data-ttu-id="efa66-169">`ValidationErrors`は、エンティティ型によっ`DbEntityValidationResult`てにグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-169">`ValidationErrors` are grouped by entity type into `DbEntityValidationResult`.</span></span> <span data-ttu-id="efa66-170">このコードは、最初に`DbEntityValidationResult`メソッドによって返されたを通じ`DbValidationError`て、次に内部で反復処理します。</span><span class="sxs-lookup"><span data-stu-id="efa66-170">The code iterates first through the `DbEntityValidationResult`s returned by the method and then through each `DbValidationError` inside.</span></span>

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

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="efa66-171">検証を使用する場合のその他の考慮事項</span><span class="sxs-lookup"><span data-stu-id="efa66-171">Other considerations when using validation</span></span>

<span data-ttu-id="efa66-172">Entity Framework 検証を使用する場合は、次の点を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="efa66-172">Here are a few other points to consider when using Entity Framework validation:</span></span>

- <span data-ttu-id="efa66-173">遅延読み込みは検証中に無効になります</span><span class="sxs-lookup"><span data-stu-id="efa66-173">Lazy loading is disabled during validation</span></span>
- <span data-ttu-id="efa66-174">EF は、マップされていないプロパティ (データベース内の列にマップされていないプロパティ) のデータ注釈を検証します。</span><span class="sxs-lookup"><span data-stu-id="efa66-174">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database)</span></span>
- <span data-ttu-id="efa66-175">検証は、の間に変更が`SaveChanges`検出された後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="efa66-175">Validation is performed after changes are detected during `SaveChanges`.</span></span> <span data-ttu-id="efa66-176">検証中に変更を行う場合は、変更トラッカーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="efa66-176">If you make changes during validation it is your responsibility to notify the change tracker</span></span>
- <span data-ttu-id="efa66-177">`DbUnexpectedValidationException`検証中にエラーが発生した場合にスローされます</span><span class="sxs-lookup"><span data-stu-id="efa66-177">`DbUnexpectedValidationException` is thrown if errors occur during validation</span></span>
- <span data-ttu-id="efa66-178">クラスにデータ注釈がない場合や、EF デザイナーを使用してモデルを作成した場合でも、モデルに含まれている Entity Framework ファセット (最大長、必須など) によって検証が行われます。</span><span class="sxs-lookup"><span data-stu-id="efa66-178">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are no data annotations in your classes and/or you used the EF Designer to create your model</span></span>
- <span data-ttu-id="efa66-179">優先順位の規則:</span><span class="sxs-lookup"><span data-stu-id="efa66-179">Precedence rules:</span></span>
  - <span data-ttu-id="efa66-180">Fluent API 呼び出しは、対応するデータ注釈をオーバーライドします</span><span class="sxs-lookup"><span data-stu-id="efa66-180">Fluent API calls override the corresponding data annotations</span></span>
- <span data-ttu-id="efa66-181">実行順序:</span><span class="sxs-lookup"><span data-stu-id="efa66-181">Execution order:</span></span>
  - <span data-ttu-id="efa66-182">プロパティの検証は、型の検証の前に行われます</span><span class="sxs-lookup"><span data-stu-id="efa66-182">Property validation occurs before type validation</span></span>
  - <span data-ttu-id="efa66-183">型の検証は、プロパティの検証が成功した場合にのみ発生します</span><span class="sxs-lookup"><span data-stu-id="efa66-183">Type validation only occurs if property validation succeeds</span></span>
- <span data-ttu-id="efa66-184">プロパティが複雑な場合、その検証にも次の内容が含まれます。</span><span class="sxs-lookup"><span data-stu-id="efa66-184">If a property is complex, its validation will also include:</span></span>
  - <span data-ttu-id="efa66-185">複合型プロパティのプロパティレベルの検証</span><span class="sxs-lookup"><span data-stu-id="efa66-185">Property-level validation on the complex type properties</span></span>
  - <span data-ttu-id="efa66-186">複合型の検証を含む`IValidatableObject`複合型の型レベルの検証</span><span class="sxs-lookup"><span data-stu-id="efa66-186">Type level validation on the complex type, including `IValidatableObject` validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="efa66-187">Summary</span><span class="sxs-lookup"><span data-stu-id="efa66-187">Summary</span></span>

<span data-ttu-id="efa66-188">Entity Framework の検証 API は、MVC でのクライアント側の検証に非常に適していますが、クライアント側の検証に依存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="efa66-188">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="efa66-189">Entity Framework は、code first Fluent API で適用した DataAnnotations または構成のサーバー側での検証を処理します。</span><span class="sxs-lookup"><span data-stu-id="efa66-189">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="efa66-190">また、 `IValidatableObject`インターフェイスを使用するか、 `DbContext.ValidateEntity`メソッドをタップするかにかかわらず、動作をカスタマイズするための拡張ポイントがいくつか表示されています。</span><span class="sxs-lookup"><span data-stu-id="efa66-190">You also saw a number of extensibility points for customizing the behavior whether you use the `IValidatableObject` interface or tap into the `DbContext.ValidateEntity` method.</span></span> <span data-ttu-id="efa66-191">また、Code First、Model First または Database First のワークフローを`DbContext`使用して概念モデルを記述するかどうかによって、これらの検証の2つの方法がで利用できます。</span><span class="sxs-lookup"><span data-stu-id="efa66-191">And these last two means of validation are available through the `DbContext`, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
