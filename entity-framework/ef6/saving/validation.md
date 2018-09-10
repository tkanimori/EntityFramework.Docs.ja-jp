---
title: EF6 の検証
author: divega
ms.date: 2016-10-23
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 65639b0f91f54ee2cd1336f6b6cd4caf45ede680
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251025"
---
# <a name="data-validation"></a><span data-ttu-id="9a21a-102">データの検証</span><span class="sxs-lookup"><span data-stu-id="9a21a-102">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="9a21a-103">**EF4.1 以降のみ**-機能、Api、Entity Framework 4.1 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="9a21a-104">一部またはすべての情報は適用されませんが、以前のバージョンを使用している場合</span><span class="sxs-lookup"><span data-stu-id="9a21a-104">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="9a21a-105">このページのコンテンツの出典元し、Julie Lerman が最初に書き込まれた情報の記事 ([http://thedatafarm.com](http://thedatafarm.com))。</span><span class="sxs-lookup"><span data-stu-id="9a21a-105">The content on this page is adapted from and article originally written by Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)).</span></span>

<span data-ttu-id="9a21a-106">Entity Framework では、さまざまなクライアント側検証用のユーザー インターフェイスをフィードできますまたはサーバー側の検証に使用される検証機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-106">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="9a21a-107">最初にコードを使用している場合は、注釈または fluent API 構成を使用して検証を指定できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-107">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="9a21a-108">追加の検証、およびより複雑なコードで指定することができ、、最初に、メイン コードからモデルかどうかを最初にモデルまたはデータベースの最初。</span><span class="sxs-lookup"><span data-stu-id="9a21a-108">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="9a21a-109">モデル</span><span class="sxs-lookup"><span data-stu-id="9a21a-109">The model</span></span>

<span data-ttu-id="9a21a-110">クラスの単純なペアに検証を紹介します。 ブログや投稿します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-110">I’ll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

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
## <a name="data-annotations"></a><span data-ttu-id="9a21a-111">データの注釈</span><span class="sxs-lookup"><span data-stu-id="9a21a-111">Data Annotations</span></span>

<span data-ttu-id="9a21a-112">まず、コードは、コードの最初のクラスの設定の 1 つの手段として System.ComponentModel.DataAnnotations アセンブリからの注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-112">Code First uses annotations from the System.ComponentModel.DataAnnotations assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="9a21a-113">これらの注釈の間で必要な場合は、MaxLength、MinLength などのルールが提供されるは。</span><span class="sxs-lookup"><span data-stu-id="9a21a-113">Among these annotations are those which provide rules such as the Required, MaxLength and MinLength.</span></span> <span data-ttu-id="9a21a-114">多数の .NET クライアント アプリケーションでは、これらの注釈では、たとえば、ASP.NET MVC も認識します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-114">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="9a21a-115">両方クライアント側とサーバー側の検証では、これらの注釈を実現できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-115">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="9a21a-116">たとえば、ブログのタイトル プロパティに必要なプロパティを強制できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-116">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="9a21a-117">コードを追加またはアプリケーションのマークアップを変更なしに、既存の MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージの構築も動的に、クライアント側の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-117">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![図 1](~/ef6/media/figure01.png)

<span data-ttu-id="9a21a-119">投稿内 back のこのビューを作成するメソッド、Entity Framework を使用して、データベースに新しいブログを保存するが、アプリケーションがそのコードに到達する前に、MVC のクライアント側の検証がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-119">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC’s client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="9a21a-120">クライアント側の検証は堅牢なただしします。</span><span class="sxs-lookup"><span data-stu-id="9a21a-120">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="9a21a-121">ユーザーが自分のブラウザーの機能に影響を与えるかさらに悪いことまだ、ハッカー可能性がありますを使用してちょっとしたテクニックがいくつか UI 検証を回避します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-121">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="9a21a-122">Entity Framework また必要な注釈を認識し、それを検証します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-122">But Entity Framework will also recognize the Required annotation and validate it.</span></span>

<span data-ttu-id="9a21a-123">これをテストする簡単な方法では、MVC のクライアント側の検証機能を無効にします。</span><span class="sxs-lookup"><span data-stu-id="9a21a-123">A simple way to test this is to disable MVC’s client-side validation feature.</span></span> <span data-ttu-id="9a21a-124">MVC アプリケーションの web.config ファイルで、これを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-124">You can do this in the MVC application’s web.config file.</span></span> <span data-ttu-id="9a21a-125">AppSettings セクションには、ClientValidationEnabled のキーがあります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-125">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="9a21a-126">このキーを false に設定すると、UI が検証を実行することはできなくなります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-126">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
    <appSettings>
        <add key="ClientValidationEnabled"value="false"/>
        ...
    </appSettings>
```

<span data-ttu-id="9a21a-127">クライアント側検証を無効にしてを使用しても、アプリケーションで同じ応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-127">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="9a21a-128">エラー メッセージ「タイトル フィールドが必要」として表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-128">The error message “The Title field is required” will be displayed as.</span></span> <span data-ttu-id="9a21a-129">これ以外のサーバー側の検証の結果があります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-129">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="9a21a-130">Entity Framework は、に対して検証を必要な注釈 (がビルドされ、データベースに送信する INSERT コマンドにも面倒) する前に mvc メッセージが表示されるエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-130">Entity Framework will perform the validation on the Required annotation (before it even bothers to build and INSERT command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="9a21a-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9a21a-131">Fluent API</span></span>

<span data-ttu-id="9a21a-132">コードを使用する側とサーバーは、同じクライアントを取得する注釈ではなく最初の fluent API 側の検証。</span><span class="sxs-lookup"><span data-stu-id="9a21a-132">You can use code first’s fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="9a21a-133">必要な使用をご紹介します MaxLength 検証を使用してこのなく。</span><span class="sxs-lookup"><span data-stu-id="9a21a-133">Rather than use Required, I’ll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="9a21a-134">コードはまず、クラスからモデルを構築は、Fluent API 構成が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-134">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="9a21a-135">DbContext クラスの OnModelCreating メソッドをオーバーライドすることで、構成を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-135">You can inject the configurations by overriding the DbContext class’ OnModelCreating  method.</span></span> <span data-ttu-id="9a21a-136">BloggerName プロパティを 10 文字以内にできることを指定する構成を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-136">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

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

<span data-ttu-id="9a21a-137">検証エラーがスローされた Fluent API 構成に基づいたは自動的に reach UI ことができますキャプチャしません、コードと次の応答でそれに応じて。</span><span class="sxs-lookup"><span data-stu-id="9a21a-137">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="9a21a-138">ここでの例外が処理エラー コードは、Entity Framework が 10 文字の最大値を超える BloggerName でブログを保存しようとしたときに、その検証エラーをキャプチャするアプリケーションの BlogController クラスにいくつか。</span><span class="sxs-lookup"><span data-stu-id="9a21a-138">Here’s some exception handling error code in the application’s BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

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

<span data-ttu-id="9a21a-139">検証は、ModelState.AddModelError を使用する追加コードが使用されている理由は、このビューに再び渡さ自動的にしません。</span><span class="sxs-lookup"><span data-stu-id="9a21a-139">The validation doesn’t automatically get passed back into the view which is why the additional code that uses ModelState.AddModelError is being used.</span></span> <span data-ttu-id="9a21a-140">これにより、ビューを使用して、作成された ValidationMessageFor Htmlhelper エラーを表示するにように、エラーの詳細。</span><span class="sxs-lookup"><span data-stu-id="9a21a-140">This ensures that the error details make it to the view which will then use the ValidationMessageFor Htmlhelper to display the error.</span></span>

``` csharp
    @Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="9a21a-141">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9a21a-141">IValidatableObject</span></span>

<span data-ttu-id="9a21a-142">IValidatableObject は、System.ComponentModel.DataAnnotations に存在するインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="9a21a-142">IValidatableObject is an interface that lives in System.ComponentModel.DataAnnotations.</span></span> <span data-ttu-id="9a21a-143">Entity Framework API の一部ではありませんが、利用でき、サーバー側の検証の Entity Framework のクラスで。</span><span class="sxs-lookup"><span data-stu-id="9a21a-143">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="9a21a-144">Entity Framework を呼び出すか、SaveChanges 中に検証メソッド呼び出すことができます自分でいつでも、クラスを検証する IValidatableObject を提供します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-144">IValidatableObject provides a Validate method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="9a21a-145">など、必要な構成と MaxLength は、1 つのフィールド検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-145">Configurations such as Required and MaxLength perform validaton on a single field.</span></span> <span data-ttu-id="9a21a-146">検証メソッドには 2 つのフィールドを比較するより複雑なロジックができます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-146">In the Validate method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="9a21a-147">次の例では、IValidatableObject を実装し、タイトルと BloggerName ことはできませんと一致する規則を指定するブログ クラスが拡張されました。</span><span class="sxs-lookup"><span data-stu-id="9a21a-147">In the following example, the Blog class has been extended to implement IValidatableObject and then provide a rule that the Title and BloggerName cannot match.</span></span>

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

<span data-ttu-id="9a21a-148">ValidationResult コンス トラクターでは、エラー メッセージと、検証に関連付けられているメンバー名を表す文字列の配列を表す文字列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-148">The ValidationResult constructor takes a string that represents the error message and an array of strings that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="9a21a-149">この検証では、タイトルと、BloggerName の両方をチェック、ため両方のプロパティ名が返されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-149">Since this validation checks both the Title and the BloggerName, both property names are returned.</span></span>

<span data-ttu-id="9a21a-150">Fluent API によって提供される検証とは異なり、ビューでこの検証の結果が認識され、ModelState にエラーを追加する前に使用した例外ハンドラーが必要ではありません。</span><span class="sxs-lookup"><span data-stu-id="9a21a-150">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into ModelState is unnecessary.</span></span> <span data-ttu-id="9a21a-151">ValidationResult で両方のプロパティ名を設定するため、これらのプロパティの両方のエラー メッセージが表示 MVC HtmlHelpers します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-151">Because I set both property names in the ValidationResult, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![図 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="9a21a-153">DbContext.ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="9a21a-153">DbContext.ValidateEntity</span></span>

<span data-ttu-id="9a21a-154">DbContext では、ValidateEntity と呼ばれる、オーバーライド可能なメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-154">DbContext has an Overridable method called ValidateEntity.</span></span> <span data-ttu-id="9a21a-155">SaveChanges を呼び出すときに Entity Framework は、キャッシュの状態が変更されていない各エンティティに対してこのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-155">When you call SaveChanges, Entity Framework will call this method for each entity in its cache whose state is not Unchanged.</span></span> <span data-ttu-id="9a21a-156">、たとえば、前のセクションで追加 Blog.Validate メソッドを呼び出すには、このメソッドで、ここでも使用を直接検証ロジックを格納できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-156">You can put validation logic directly in here or even use this method to call, for example, the Blog.Validate method added in the previous section.</span></span>

<span data-ttu-id="9a21a-157">新しい投稿への投稿のタイトルが既に使用されていないことを確認することを検証する ValidateEntity 上書きの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-157">Here’s an example of a ValidateEntity override that validates new Posts to ensure that the post title hasn’t been used already.</span></span> <span data-ttu-id="9a21a-158">エンティティは、投稿、かどうかと、状態が追加されたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-158">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="9a21a-159">場合は、し、検索、データベース内に同じタイトルの投稿があるかどうかを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a21a-159">If that’s the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="9a21a-160">既に既存の投稿がある、新しい DbEntityValidationResult が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-160">If there is an existing post already, then a new DbEntityValidationResult is created.</span></span>

<span data-ttu-id="9a21a-161">DbEntityValidationResult には、対象の DbEntityEntry と単一のエンティティの ICollection の DbValidationErrors が格納されています。</span><span class="sxs-lookup"><span data-stu-id="9a21a-161">DbEntityValidationResult houses a DbEntityEntry and an ICollection of DbValidationErrors for a single entity.</span></span> <span data-ttu-id="9a21a-162">このメソッドの先頭を DbEntityValidationResult がインスタンス化され、その ValidationErrors コレクションに検出されたエラーを追加する、します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-162">At the start of this method, a  DbEntityValidationResult is instantiated and then any errors that are discovered are added into its ValidationErrors collection.</span></span>

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

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="9a21a-163">明示的に検証をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="9a21a-163">Explicitly triggering validation</span></span>

<span data-ttu-id="9a21a-164">SaveChanges への呼び出しは、この記事で説明する検証のすべてをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="9a21a-164">A call to SaveChanges triggers all of the validations covered in this article.</span></span> <span data-ttu-id="9a21a-165">ただし、SaveChanges に依存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9a21a-165">But you don’t need to rely on SaveChanges.</span></span> <span data-ttu-id="9a21a-166">アプリケーション内の他の場所を検証することもできます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-166">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="9a21a-167">DbContext.GetValidationErrors はすべての検証、注釈または Fluent API で定義されている、IValidatableObject (たとえば、Blog.Validate) で作成された、検証、DbContext.ValidateEntity で実行される検証をトリガーします。メソッド。</span><span class="sxs-lookup"><span data-stu-id="9a21a-167">DbContext.GetValidationErrors will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in IValidatableObject (for example, Blog.Validate), and the validations performed in the DbContext.ValidateEntity method.</span></span>

<span data-ttu-id="9a21a-168">次のコードでは、DbContext の現在のインスタンスで GetValidationErrors を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-168">The following code will call GetValidationErrors on the current instance of a DbContext.</span></span> <span data-ttu-id="9a21a-169">ValidationErrors は DbValidationRestuls にエンティティの種類でグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-169">ValidationErrors are grouped by entity type into DbValidationRestuls.</span></span> <span data-ttu-id="9a21a-170">メソッドによって返される DbValidationResults とし、内の各 ValidationError を介して、コードが最初に反復処理します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-170">The code iterates first through the DbValidationResults returned by the method and then through each ValidationError inside.</span></span>

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

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="9a21a-171">検証を使用する場合に、その他の考慮事項</span><span class="sxs-lookup"><span data-stu-id="9a21a-171">Other considerations when using validation</span></span>

<span data-ttu-id="9a21a-172">Entity Framework の検証を使用する際に考慮するその他のいくつかの点を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-172">Here are a few other points to consider when using Entity Framework validation:</span></span>

-   <span data-ttu-id="9a21a-173">検証中には、遅延読み込みが無効です。</span><span class="sxs-lookup"><span data-stu-id="9a21a-173">Lazy loading is disabled during validation.</span></span>
-   <span data-ttu-id="9a21a-174">EF では、データ注釈で、マップされていないプロパティ (データベース内の列にマップされていないプロパティ) を検証します。</span><span class="sxs-lookup"><span data-stu-id="9a21a-174">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database).</span></span>
-   <span data-ttu-id="9a21a-175">検証は、SaveChanges 中に変更が検出された後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-175">Validation is performed after changes are detected during SaveChanges.</span></span> <span data-ttu-id="9a21a-176">検証中に変更を加えた場合は、変更トラッカーに通知する責任を勧めします。</span><span class="sxs-lookup"><span data-stu-id="9a21a-176">If you make changes during validation it is your responsibility to notify the change tracker.</span></span>
-   <span data-ttu-id="9a21a-177">検証中にエラーが発生した場合、DbUnexpectedValidationException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-177">DbUnexpectedValidationException is thrown if errors occur during validation.</span></span>
-   <span data-ttu-id="9a21a-178">(必要な最大の長さなど)、モデルに Entity Framework を含むファセットには、クラスのデータの注釈がないや、モデルの作成に EF Designer を使用した場合でも、検証になります。</span><span class="sxs-lookup"><span data-stu-id="9a21a-178">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are not data annotations on your classes and/or you used the EF Designer to create your model.</span></span>
-   <span data-ttu-id="9a21a-179">優先順位の規則:</span><span class="sxs-lookup"><span data-stu-id="9a21a-179">Precedence rules:</span></span>
    -   <span data-ttu-id="9a21a-180">Fluent API の呼び出しが対応するデータ注釈を上書き</span><span class="sxs-lookup"><span data-stu-id="9a21a-180">Fluent API calls override the corresponding data annotations</span></span>
-   <span data-ttu-id="9a21a-181">実行の順序:</span><span class="sxs-lookup"><span data-stu-id="9a21a-181">Execution order:</span></span>
    -   <span data-ttu-id="9a21a-182">プロパティの検証は、型の検証の前に発生します</span><span class="sxs-lookup"><span data-stu-id="9a21a-182">Property validation occurs before type validation</span></span>
    -   <span data-ttu-id="9a21a-183">型の検証は、プロパティの検証が成功した場合にのみ発生します</span><span class="sxs-lookup"><span data-stu-id="9a21a-183">Type validation only occurs if property validation succeeds</span></span>
-   <span data-ttu-id="9a21a-184">プロパティが複雑な場合、検証も含まれます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-184">If a property is complex its validation will also include:</span></span>
    -   <span data-ttu-id="9a21a-185">複合型のプロパティに対するプロパティ レベルの検証</span><span class="sxs-lookup"><span data-stu-id="9a21a-185">Property-level validation on the complex type properties</span></span>
    -   <span data-ttu-id="9a21a-186">複合型 IValidatableObject 評価を含む、複合型で型レベルの検証</span><span class="sxs-lookup"><span data-stu-id="9a21a-186">Type level validation on the complex type, including IValidatableObject validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="9a21a-187">まとめ</span><span class="sxs-lookup"><span data-stu-id="9a21a-187">Summary</span></span>

<span data-ttu-id="9a21a-188">MVC でのクライアント側の検証と Entity Framework での検証 API が非常にうまく再生しますが、クライアント側の検証に依存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9a21a-188">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="9a21a-189">Entity Framework は、DataAnnotations または code first Fluent API で適用した構成の検証をサーバー側での考慮されます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-189">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="9a21a-190">IValidatableObject インターフェイスを使用してまたは DbContet.ValidateEntity メソッドをタップするかどうか、動作をカスタマイズするための機能拡張ポイント数も確認しました。</span><span class="sxs-lookup"><span data-stu-id="9a21a-190">You also saw a number of extensibility points for customizing the behavior whether you use the IValidatableObject interface or tap into the DbContet.ValidateEntity method.</span></span> <span data-ttu-id="9a21a-191">Code First、Model First または Database First ワークフローを使用して、概念モデルを記述するかどうかは、これらの検証の最後の 2 つの方法は、DbContext から入手できます。</span><span class="sxs-lookup"><span data-stu-id="9a21a-191">And these last two means of validation are available through the DbContext, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
