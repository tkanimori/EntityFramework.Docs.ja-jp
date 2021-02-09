---
title: EF Core と Xamarin の概要 - EF Core
description: Xamarin を使用して Entity Framework Core でモバイル アプリを作成するための概要チュートリアル
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: e4a26d7a0c6909002be0974ee9fe1c5db7c1046a
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983600"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>EF Core と Xamarin の概要

このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) アプリケーションを作成します。

Windows 上の Visual Studio または Visual Studio for Mac を使用すると、このチュートリアルを実行できます。

> [!TIP]
> この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

次のいずれかをインストールします。

* 次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):
  * **.NET によるモバイル開発**
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

この[ドキュメントでは、各プラットフォームの詳細なインストール手順について説明](/xamarin/get-started/installation)します。

## <a name="download-and-run-the-sample-project"></a>サンプル プロジェクトのダウンロードと実行

このサンプル アプリケーションを実行して調べるには、GitHub からコードをダウンロードします。

ダウンロードしたら、ソリューション ファイル `EFGettingStarted.sln` を Visual Studio または Visual Studio for Mac で開き、選択したプラットフォーム上でアプリケーションを実行します。

アプリを初めて起動すると、ブログを表す 2 つのエントリがローカルの SQLite データベースに読み込まれます。

![すべてのブログ リスト ページのスクリーンショット](_static/xamarin-tutorial-1.png)

ツール バーの **[追加]** ボタンをクリックします。

新しいブログに関する情報を入力できる新しいページが表示されます。

![新しいブログの編集ページのスクリーンショット](_static/xamarin-tutorial-2.png)

すべての情報を入力したら、ツール バーの **[保存]** をクリックします。 新しいブログがアプリの SQLite データベースに保存され、リストに表示されます。

リスト内のブログ エントリのいずれかをクリックすると、そのブログのすべての投稿が表示されます。

![ブログ記事リスト ページのスクリーンショット](_static/xamarin-tutorial-3.png)

ツール バーの **[追加]** をクリックします。

新しいブログ記事に関する情報を入力できるページが表示されます。

![新しい投稿の追加ページのスクリーンショット](_static/xamarin-tutorial-4.png)

すべての情報を入力したら、ツール バーの **[保存]** をクリックします。

新しい投稿は、前の手順でクリックしたブログ記事に関連付けられ、アプリの SQLite データベースに保存され、リストに表示されます。

ブログ リスト ページに戻ります。 ツール バーの **[すべて削除]** をクリックします。 すべてのブログとそれに対応する投稿は、アプリの SQLite データベースから削除されます。

![すべてのブログが削除されたアプリのスクリーンショット](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>コードを調べる

次のセクションでは、Xamarin.Forms で EF Core を使用して SQLite データベースからデータの読み取り、作成、更新、削除を行うサンプル プロジェクトのコードについて説明します。

[データの表示](/xamarin/xamarin-forms/app-fundamentals/data-binding/)および[ページ間の移動](/xamarin/xamarin-forms/app-fundamentals/navigation/)に関する Xamarin.Forms トピックを理解していることを前提として説明します。

> [!IMPORTANT]
> Entity Framework Core ではリフレクションを使用して、**リリース** モード構成中は Xamarin.iOS リンカーによって削除できる関数が呼び出されます。 これは、次の 2 つの方法のいずれかで回避できます。
>
> * 1 つ目は、**iOS ビルド** オプションの **追加 mtouch 引数** に `--linkskip System.Core` を追加することです。
> * または、**iOS ビルド** オプションで Xamarin.iOS の **[リンカーの動作]** を `Don't Link` に設定します。
> [こちらの記事に、Xamarin.iOS リンカーについての詳細が記載されています](/xamarin/ios/deploy-test/linker) (Xamarin.iOS の動作を設定する方法など)。 (この方法は、ストアからの拒否が発生する可能性があるため、理想的ではありません)。

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet パッケージ

EF Core を使用して Xamarin.Forms アプリを作成するには、対象とする EF Core データベース プロバイダー用のパッケージを、Xamarin.Forms ソリューションのすべてのプロジェクトにインストールします。 このチュートリアルでは、SQLite プロバイダーを使用します。

Xamarin.Forms ソリューションの各プロジェクトには、次の NuGet パッケージが必要です。

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>モデル クラス。

EF Core 経由でアクセスされる SQLite データベースの各テーブルは、クラスでモデル化されます。 このサンプルでは、`Blog` と `Post` という 2 つのクラスが使用されています。これらは `Models` フォルダー内にあります。

モデル クラスは、データベースの列のモデル化を行うプロパティでのみ構成されます。

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* `Posts` プロパティには、`Blog` と `Post` 間の親子関係を定義します。

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* `BlogId` および `Blog` の各プロパティは、`Post` のインスタンスの親 `Blog` オブジェクトに関連付けられています。

## <a name="data-context"></a>データ コンテキスト

`BloggingContext` クラスは `Services` フォルダー内にあり、EF Core `DbContext` クラスから継承されます。 `DbContext` は、データベースのクエリと変更をグループ化するために使用されます。

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* 型 `DbSet` を持つこのクラスの両プロパティは、ブログと投稿を表す基となるテーブルを操作するために使用されます。
* iOS 上で SQLite を開始するには、コンストラクターに `SQLitePCL.Batteries_V2.Init()` が必要です。
* `OnConfiguring` 関数を使用して、物理デバイス上に SQLite データベースの場所を設定します。

## <a name="create-read-update--delete"></a>作成、読み取り、更新、および削除

EF Core を使用して SQLite にアクセスするアプリのインスタンスの一部を次に示します。

### <a name="read"></a>読み取り

* すべてのレコードが返されます。
  * `BlogsPage.xaml.cs` の `OnAppearing` 関数を使用すると、すべての `Blog` レコードが返され、それらが `List` 変数に格納されます。

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* 特定のレコードが返されます。
  * `PostsPage.xaml.cs` の `OnAppearing` 関数を使用すると、特定の `BlogId` を含む `Post` レコードが返されます。

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>作成

* 新しいレコードが挿入されます。
  * `AddBlogPage.xaml.cs` の `Save_Clicked` 関数を使用すると、新しい `Blog` オブジェクトが SQLite データベースに挿入されます。

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>更新

* 既存のレコードが更新されます。
  * `AddPostPage.xaml.cs` の `Save_Clicked` 関数を使用すると、既存の `Blog` オブジェクトが新しい `Post` で更新されます。

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

### <a name="delete"></a>削除

* 子レコードへのカスケードを含むすべてのレコードが削除されます。
  * `BlogsPage.xaml.cs` の `DeleteAll_Clicked` 関数を使用すると、SQLite データベース内のすべての `Blog` レコードが削除され、その削除がすべての `Blog` の子 `Post` レコードにカスケードされます。

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>次の手順

この概要では、Xamarin.Forms アプリケーションを使用し、Entity Framework Core を使用して SQLite データベースにアクセスする方法について学習しました。

Xamarin 開発者にお勧めのその他の Entity Framework Core トピック:

* [`DbContext` の構成](xref:core/dbcontext-configuration/index)
* [LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください
* [required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します
