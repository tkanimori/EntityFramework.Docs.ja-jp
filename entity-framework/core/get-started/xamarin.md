---
title: EF Core と Xamarin の概要 - EF Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 14fe61e0ddd648f824bfe3c1c91a58af91b1c2d4
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452995"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="eb0f1-102">EF Core と Xamarin の概要</span><span class="sxs-lookup"><span data-stu-id="eb0f1-102">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="eb0f1-103">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-103">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="eb0f1-104">Windows 上の Visual Studio または Visual Studio for Mac を使用すると、このチュートリアルを実行できます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-104">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="eb0f1-105">この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-105">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb0f1-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="eb0f1-106">Prerequisites</span></span>

<span data-ttu-id="eb0f1-107">次のいずれかをインストールします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-107">Install one of the below:</span></span>

* <span data-ttu-id="eb0f1-108">次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="eb0f1-108">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="eb0f1-109">**.NET によるモバイル開発**</span><span class="sxs-lookup"><span data-stu-id="eb0f1-109">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="eb0f1-110">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="eb0f1-110">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="eb0f1-111">この[ドキュメントでは、各プラットフォームの詳細なインストール手順について説明](/xamarin/get-started/installation)します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-111">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="eb0f1-112">サンプル プロジェクトのダウンロードと実行</span><span class="sxs-lookup"><span data-stu-id="eb0f1-112">Download and run the sample project</span></span>

<span data-ttu-id="eb0f1-113">このサンプル アプリケーションを実行して調べるには、GitHub からコードをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-113">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="eb0f1-114">ダウンロードしたら、ソリューション ファイル `EFGettingStarted.sln` を Visual Studio または Visual Studio for Mac で開き、選択したプラットフォーム上でアプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-114">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="eb0f1-115">アプリを初めて起動すると、ブログを表す 2 つのエントリがローカルの SQLite データベースに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-115">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![すべてのブログ リスト ページのスクリーンショット](_static/xamarin-tutorial-1.png)

<span data-ttu-id="eb0f1-117">ツール バーの **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-117">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="eb0f1-118">新しいブログに関する情報を入力できる新しいページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-118">A new page will appear that allows you to enter information about a new blog.</span></span>

![新しいブログの編集ページのスクリーンショット](_static/xamarin-tutorial-2.png)

<span data-ttu-id="eb0f1-120">すべての情報を入力したら、ツール バーの **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-120">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="eb0f1-121">新しいブログがアプリの SQLite データベースに保存され、リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-121">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="eb0f1-122">リスト内のブログ エントリのいずれかをクリックすると、そのブログのすべての投稿が表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-122">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![ブログ記事リスト ページのスクリーンショット](_static/xamarin-tutorial-3.png)

<span data-ttu-id="eb0f1-124">ツール バーの **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-124">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="eb0f1-125">新しいブログ記事に関する情報を入力できるページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-125">A page then appears that allows you to fill out information about a new blog post.</span></span>

![新しい投稿の追加ページのスクリーンショット](_static/xamarin-tutorial-4.png)

<span data-ttu-id="eb0f1-127">すべての情報を入力したら、ツール バーの **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-127">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="eb0f1-128">新しい投稿は、前の手順でクリックしたブログ記事に関連付けられ、アプリの SQLite データベースに保存され、リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-128">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="eb0f1-129">ブログ リスト ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-129">Go back to the blog list page.</span></span> <span data-ttu-id="eb0f1-130">ツール バーの **[すべて削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-130">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="eb0f1-131">すべてのブログとそれに対応する投稿は、アプリの SQLite データベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-131">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![すべてのブログが削除されたアプリのスクリーンショット](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="eb0f1-133">コードを調べる</span><span class="sxs-lookup"><span data-stu-id="eb0f1-133">Explore the code</span></span>

<span data-ttu-id="eb0f1-134">次のセクションでは、Xamarin.Forms で EF Core を使用して SQLite データベースからデータの読み取り、作成、更新、削除を行うサンプル プロジェクトのコードについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-134">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="eb0f1-135">[データの表示](/xamarin/xamarin-forms/app-fundamentals/data-binding/)および[ページ間の移動](/xamarin/xamarin-forms/app-fundamentals/navigation/)に関する Xamarin.Forms トピックを理解していることを前提として説明します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-135">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="eb0f1-136">Entity Framework Core NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="eb0f1-136">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="eb0f1-137">EF Core を使用して Xamarin.Forms アプリを作成するには、対象とする EF Core データベース プロバイダー用のパッケージを、Xamarin.Forms ソリューションのすべてのプロジェクトにインストールします。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-137">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="eb0f1-138">このチュートリアルでは、SQLite プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-138">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="eb0f1-139">Xamarin.Forms ソリューションの各プロジェクトには、次の NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-139">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="eb0f1-140">モデル クラス。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-140">Model classes</span></span>

<span data-ttu-id="eb0f1-141">EF Core 経由でアクセスされる SQLite データベースの各テーブルは、クラスでモデル化されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-141">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="eb0f1-142">このサンプルでは、`Blog` と `Post` という 2 つのクラスが使用されています。これらは `Models` フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-142">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="eb0f1-143">モデル クラスは、データベースの列のモデル化を行うプロパティでのみ構成されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-143">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="eb0f1-144">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="eb0f1-144">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="eb0f1-145">`Posts` プロパティには、`Blog` と `Post` 間の親子関係を定義します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-145">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="eb0f1-146">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="eb0f1-146">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="eb0f1-147">`BlogId` および `Blog` の各プロパティは、`Post` のインスタンスの親 `Blog` オブジェクトに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-147">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="eb0f1-148">データ コンテキスト</span><span class="sxs-lookup"><span data-stu-id="eb0f1-148">Data context</span></span>

<span data-ttu-id="eb0f1-149">`BloggingContext` クラスは `Services` フォルダー内にあり、EF Core `DbContext` クラスから継承されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-149">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="eb0f1-150">`DbContext` は、データベースのクエリと変更をグループ化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-150">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="eb0f1-151">型 `DbSet` を持つこのクラスの両プロパティは、ブログと投稿を表す基となるテーブルを操作するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-151">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="eb0f1-152">iOS 上で SQLite を開始するには、コンストラクターに `SQLitePCL.Batteries_V2.Init()` が必要です。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-152">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="eb0f1-153">`OnConfiguring` 関数を使用して、物理デバイス上に SQLite データベースの場所を設定します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-153">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="eb0f1-154">作成、読み取り、更新、および削除</span><span class="sxs-lookup"><span data-stu-id="eb0f1-154">Create, read, update & delete</span></span>

<span data-ttu-id="eb0f1-155">EF Core を使用して SQLite にアクセスするアプリのインスタンスの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-155">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="eb0f1-156">読み取り</span><span class="sxs-lookup"><span data-stu-id="eb0f1-156">Read</span></span>

* <span data-ttu-id="eb0f1-157">すべてのレコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-157">Return all records.</span></span>
  * <span data-ttu-id="eb0f1-158">`BlogsPage.xaml.cs` の `OnAppearing` 関数を使用すると、すべての `Blog` レコードが返され、それらが `List` 変数に格納されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-158">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="eb0f1-159">特定のレコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-159">Return specific records.</span></span>
  * <span data-ttu-id="eb0f1-160">`PostsPage.xaml.cs` の `OnAppearing` 関数を使用すると、特定の `BlogId` を含む `Post` レコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-160">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="eb0f1-161">作成</span><span class="sxs-lookup"><span data-stu-id="eb0f1-161">Create</span></span>

* <span data-ttu-id="eb0f1-162">新しいレコードが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-162">Insert a new record.</span></span>
  * <span data-ttu-id="eb0f1-163">`AddBlogPage.xaml.cs` の `Save_Clicked` 関数を使用すると、新しい `Blog` オブジェクトが SQLite データベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-163">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="eb0f1-164">更新</span><span class="sxs-lookup"><span data-stu-id="eb0f1-164">Update</span></span>

* <span data-ttu-id="eb0f1-165">既存のレコードが更新されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-165">Update an existing record.</span></span>
  * <span data-ttu-id="eb0f1-166">`AddPostPage.xaml.cs` の `Save_Clicked` 関数を使用すると、既存の `Blog` オブジェクトが新しい `Post` で更新されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-166">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="eb0f1-167">削除</span><span class="sxs-lookup"><span data-stu-id="eb0f1-167">Delete</span></span>

* <span data-ttu-id="eb0f1-168">子レコードへのカスケードを含むすべてのレコードが削除されます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-168">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="eb0f1-169">`BlogsPage.xaml.cs` の `DeleteAll_Clicked` 関数を使用すると、SQLite データベース内のすべての `Blog` レコードが削除され、その削除がすべての `Blog` の子 `Post` レコードにカスケードされます。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-169">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="eb0f1-170">次の手順</span><span class="sxs-lookup"><span data-stu-id="eb0f1-170">Next steps</span></span>

<span data-ttu-id="eb0f1-171">この概要では、Xamarin.Forms アプリケーションを使用し、Entity Framework Core を使用して SQLite データベースにアクセスする方法について学習しました。</span><span class="sxs-lookup"><span data-stu-id="eb0f1-171">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="eb0f1-172">Xamarin 開発者にお勧めのその他の Entity Framework Core トピック:</span><span class="sxs-lookup"><span data-stu-id="eb0f1-172">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="eb0f1-173">`DbContext` の構成</span><span class="sxs-lookup"><span data-stu-id="eb0f1-173">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="eb0f1-174">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="eb0f1-174">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="eb0f1-175">[required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します</span><span class="sxs-lookup"><span data-stu-id="eb0f1-175">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
