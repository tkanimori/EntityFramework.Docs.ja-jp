---
title: EF Core と Xamarin の概要 - EF Core
description: Xamarin を使用して Entity Framework Core でモバイル アプリを作成するための概要チュートリアル
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619285"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="a8376-103">EF Core と Xamarin の概要</span><span class="sxs-lookup"><span data-stu-id="a8376-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="a8376-104">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="a8376-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="a8376-105">Windows 上の Visual Studio または Visual Studio for Mac を使用すると、このチュートリアルを実行できます。</span><span class="sxs-lookup"><span data-stu-id="a8376-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="a8376-106">この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="a8376-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a8376-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a8376-107">Prerequisites</span></span>

<span data-ttu-id="a8376-108">次のいずれかをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a8376-108">Install one of the below:</span></span>

* <span data-ttu-id="a8376-109">次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="a8376-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="a8376-110">**.NET によるモバイル開発**</span><span class="sxs-lookup"><span data-stu-id="a8376-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="a8376-111">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a8376-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="a8376-112">この[ドキュメントでは、各プラットフォームの詳細なインストール手順について説明](/xamarin/get-started/installation)します。</span><span class="sxs-lookup"><span data-stu-id="a8376-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="a8376-113">サンプル プロジェクトのダウンロードと実行</span><span class="sxs-lookup"><span data-stu-id="a8376-113">Download and run the sample project</span></span>

<span data-ttu-id="a8376-114">このサンプル アプリケーションを実行して調べるには、GitHub からコードをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="a8376-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="a8376-115">ダウンロードしたら、ソリューション ファイル `EFGettingStarted.sln` を Visual Studio または Visual Studio for Mac で開き、選択したプラットフォーム上でアプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="a8376-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="a8376-116">アプリを初めて起動すると、ブログを表す 2 つのエントリがローカルの SQLite データベースに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a8376-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![すべてのブログ リスト ページのスクリーンショット](_static/xamarin-tutorial-1.png)

<span data-ttu-id="a8376-118">ツール バーの **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a8376-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="a8376-119">新しいブログに関する情報を入力できる新しいページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![新しいブログの編集ページのスクリーンショット](_static/xamarin-tutorial-2.png)

<span data-ttu-id="a8376-121">すべての情報を入力したら、ツール バーの **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a8376-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="a8376-122">新しいブログがアプリの SQLite データベースに保存され、リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="a8376-123">リスト内のブログ エントリのいずれかをクリックすると、そのブログのすべての投稿が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![ブログ記事リスト ページのスクリーンショット](_static/xamarin-tutorial-3.png)

<span data-ttu-id="a8376-125">ツール バーの **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a8376-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="a8376-126">新しいブログ記事に関する情報を入力できるページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![新しい投稿の追加ページのスクリーンショット](_static/xamarin-tutorial-4.png)

<span data-ttu-id="a8376-128">すべての情報を入力したら、ツール バーの **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a8376-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="a8376-129">新しい投稿は、前の手順でクリックしたブログ記事に関連付けられ、アプリの SQLite データベースに保存され、リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="a8376-130">ブログ リスト ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="a8376-130">Go back to the blog list page.</span></span> <span data-ttu-id="a8376-131">ツール バーの **[すべて削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a8376-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="a8376-132">すべてのブログとそれに対応する投稿は、アプリの SQLite データベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![すべてのブログが削除されたアプリのスクリーンショット](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="a8376-134">コードを調べる</span><span class="sxs-lookup"><span data-stu-id="a8376-134">Explore the code</span></span>

<span data-ttu-id="a8376-135">次のセクションでは、Xamarin.Forms で EF Core を使用して SQLite データベースからデータの読み取り、作成、更新、削除を行うサンプル プロジェクトのコードについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a8376-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="a8376-136">[データの表示](/xamarin/xamarin-forms/app-fundamentals/data-binding/)および[ページ間の移動](/xamarin/xamarin-forms/app-fundamentals/navigation/)に関する Xamarin.Forms トピックを理解していることを前提として説明します。</span><span class="sxs-lookup"><span data-stu-id="a8376-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="a8376-137">Entity Framework Core NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="a8376-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="a8376-138">EF Core を使用して Xamarin.Forms アプリを作成するには、対象とする EF Core データベース プロバイダー用のパッケージを、Xamarin.Forms ソリューションのすべてのプロジェクトにインストールします。</span><span class="sxs-lookup"><span data-stu-id="a8376-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="a8376-139">このチュートリアルでは、SQLite プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="a8376-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="a8376-140">Xamarin.Forms ソリューションの各プロジェクトには、次の NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="a8376-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="a8376-141">モデル クラス。</span><span class="sxs-lookup"><span data-stu-id="a8376-141">Model classes</span></span>

<span data-ttu-id="a8376-142">EF Core 経由でアクセスされる SQLite データベースの各テーブルは、クラスでモデル化されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="a8376-143">このサンプルでは、`Blog` と `Post` という 2 つのクラスが使用されています。これらは `Models` フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="a8376-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="a8376-144">モデル クラスは、データベースの列のモデル化を行うプロパティでのみ構成されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="a8376-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="a8376-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="a8376-146">`Posts` プロパティには、`Blog` と `Post` 間の親子関係を定義します。</span><span class="sxs-lookup"><span data-stu-id="a8376-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="a8376-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="a8376-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="a8376-148">`BlogId` および `Blog` の各プロパティは、`Post` のインスタンスの親 `Blog` オブジェクトに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="a8376-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="a8376-149">データ コンテキスト</span><span class="sxs-lookup"><span data-stu-id="a8376-149">Data context</span></span>

<span data-ttu-id="a8376-150">`BloggingContext` クラスは `Services` フォルダー内にあり、EF Core `DbContext` クラスから継承されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="a8376-151">`DbContext` は、データベースのクエリと変更をグループ化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="a8376-152">型 `DbSet` を持つこのクラスの両プロパティは、ブログと投稿を表す基となるテーブルを操作するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="a8376-153">iOS 上で SQLite を開始するには、コンストラクターに `SQLitePCL.Batteries_V2.Init()` が必要です。</span><span class="sxs-lookup"><span data-stu-id="a8376-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="a8376-154">`OnConfiguring` 関数を使用して、物理デバイス上に SQLite データベースの場所を設定します。</span><span class="sxs-lookup"><span data-stu-id="a8376-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="a8376-155">作成、読み取り、更新、および削除</span><span class="sxs-lookup"><span data-stu-id="a8376-155">Create, read, update & delete</span></span>

<span data-ttu-id="a8376-156">EF Core を使用して SQLite にアクセスするアプリのインスタンスの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a8376-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="a8376-157">読み取り</span><span class="sxs-lookup"><span data-stu-id="a8376-157">Read</span></span>

* <span data-ttu-id="a8376-158">すべてのレコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-158">Return all records.</span></span>
  * <span data-ttu-id="a8376-159">`BlogsPage.xaml.cs` の `OnAppearing` 関数を使用すると、すべての `Blog` レコードが返され、それらが `List` 変数に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="a8376-160">特定のレコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-160">Return specific records.</span></span>
  * <span data-ttu-id="a8376-161">`PostsPage.xaml.cs` の `OnAppearing` 関数を使用すると、特定の `BlogId` を含む `Post` レコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="a8376-162">作成</span><span class="sxs-lookup"><span data-stu-id="a8376-162">Create</span></span>

* <span data-ttu-id="a8376-163">新しいレコードが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-163">Insert a new record.</span></span>
  * <span data-ttu-id="a8376-164">`AddBlogPage.xaml.cs` の `Save_Clicked` 関数を使用すると、新しい `Blog` オブジェクトが SQLite データベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="a8376-165">更新</span><span class="sxs-lookup"><span data-stu-id="a8376-165">Update</span></span>

* <span data-ttu-id="a8376-166">既存のレコードが更新されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-166">Update an existing record.</span></span>
  * <span data-ttu-id="a8376-167">`AddPostPage.xaml.cs` の `Save_Clicked` 関数を使用すると、既存の `Blog` オブジェクトが新しい `Post` で更新されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="a8376-168">削除</span><span class="sxs-lookup"><span data-stu-id="a8376-168">Delete</span></span>

* <span data-ttu-id="a8376-169">子レコードへのカスケードを含むすべてのレコードが削除されます。</span><span class="sxs-lookup"><span data-stu-id="a8376-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="a8376-170">`BlogsPage.xaml.cs` の `DeleteAll_Clicked` 関数を使用すると、SQLite データベース内のすべての `Blog` レコードが削除され、その削除がすべての `Blog` の子 `Post` レコードにカスケードされます。</span><span class="sxs-lookup"><span data-stu-id="a8376-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="a8376-171">次の手順</span><span class="sxs-lookup"><span data-stu-id="a8376-171">Next steps</span></span>

<span data-ttu-id="a8376-172">この概要では、Xamarin.Forms アプリケーションを使用し、Entity Framework Core を使用して SQLite データベースにアクセスする方法について学習しました。</span><span class="sxs-lookup"><span data-stu-id="a8376-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="a8376-173">Xamarin 開発者にお勧めのその他の Entity Framework Core トピック:</span><span class="sxs-lookup"><span data-stu-id="a8376-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="a8376-174">`DbContext` の構成</span><span class="sxs-lookup"><span data-stu-id="a8376-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="a8376-175">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="a8376-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="a8376-176">[required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します</span><span class="sxs-lookup"><span data-stu-id="a8376-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
