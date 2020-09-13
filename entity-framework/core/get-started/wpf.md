---
title: WPF の使用を開始する - EF Core
description: Entity Framework Core で WPF を使用するための概要チュートリアル
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619304"
---
# <a name="getting-started-with-wpf"></a>WPF の概要

このステップ バイ ステップ チュートリアルでは、"メイン - 詳細" フォームで POCO 型を WPF コントロールにバインドする方法について説明します。 アプリケーションでは、Entity Framework API を使用して、データベースのデータをオブジェクトに設定し、変更を追跡し、データベースにデータを保持します。

このモデルでは、一対多のリレーションシップに関係する 2 つの型が定義されています: **Category** (プリンシパル\\メイン) と **Product** (依存\\詳細)。 WPF データ バインディング フレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスター ビューで行を選択すると、対応する子データで詳細ビューが更新されます。

このチュートリアルのスクリーンショットとコード リストは、Visual Studio 2019 16.6.5 から取得されています。

> [!TIP]
> この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF) で確認できます。

## <a name="pre-requisites"></a>前提条件

* このチュートリアルを完了するには、Visual Studio 2019 16.3 以降がインストールされ、 **.NET デスクトップ ワークロード**が選択されている必要があります。
    
    Visual Studio の最新バージョンのインストールの詳細については、「[Visual Studio のインストール](/visualstudio/install/install-visual-studio)」を参照してください。

## <a name="create-the-application"></a>アプリケーションを作成する

1. Visual Studio を開く
2. スタート ウィンドウで、**[新しいプロジェクトの作成]** を選択します。
3. "WPF" を検索し、 **[WPF アプリ (.NET Core)]** を選択して、 **[次へ]** を選択します。
4. 次の画面で、プロジェクトの名前を指定し (例: **GetStartedWPF**)、 **[作成]** を選択します。

## <a name="install-the-entity-framework-nuget-packages"></a>Entity Framework NuGet パッケージをインストールする

1. ソリューションを右クリックして、 **[ソリューションの NuGet パッケージの管理...]** を選択します。

    ![NuGet パッケージの管理](_static/wpf-tutorial-nuget.jpg)

1. 検索ボックスに、「`entityframeworkcore.sqlite`」と入力します。
1. **Microsoft.EntityFrameworkCore.Sqlite** パッケージを選択します。
1. 右側のペインでプロジェクトを確認し、 **[インストール]** をクリックします

    ![Sqlite パッケージ](_static/wpf-tutorial-sqlite.jpg)

1. 同じようにして、`entityframeworkcore.proxies` を検索し、**Microsoft.EntityFrameworkCore.Proxies** をインストールします。

> [!NOTE]
> Sqlite パッケージをインストールすると、関連する **Microsoft.EntityFrameworkCore** 基本パッケージが自動的にプルされます。 **Microsoft.EntityFrameworkCore.Proxies** パッケージでは、データの "遅延読み込み" がサポートされています。 これは、子エンティティを持つエンティティがある場合、最初の読み込みでは親のみがフェッチされることを意味します。 子エンティティにアクセスしようとすると、プロキシによって検出され、必要に応じて自動的に読み込まれます。 

## <a name="define-a-model"></a>モデルを定義する

このチュートリアルでは、"Code First" を使用してモデルを実装します。 つまり、定義した C# クラスに基づいて、EF Core によりデータベース テーブルとスキーマが作成されます。

新しいクラスを追加します。 `Product.cs` という名前を指定し、次のように設定します。

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

次に、`Category.cs` という名前のクラスを追加し、次のコードを入力します。

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

**Category** クラスの **Products** プロパティと、**Product** クラスの **Category** プロパティは、ナビゲーション プロパティです。 Entity Framework では、ナビゲーション プロパティによって、2 つのエンティティ型間のリレーションシップをナビゲートする手段が提供されます。

エンティティを定義するだけでなく、DbContext から派生して DbSet&lt;TEntity&gt; プロパティを公開するクラスを定義する必要があります。 DbSet&lt;TEntity&gt; プロパティにより、モデルに含める型をコンテキストに認識させることができます。

DbContext の派生型のインスタンスによって、実行時にエンティティ オブジェクトが管理されます。これには、オブジェクトへのデータベースのデータの設定、変更の追跡、データベースへのデータの保持が含まれます。

次のような定義の新しい `ProductContext.cs` クラスを、プロジェクトに追加します。

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet` により、データベースにマップする必要のある C# エンティティが EF Core に示されます。
* EF Core の `DbContext` を構成するには、さまざまな方法があります。 詳しくは次を参照してください: 「[DbContext の構成](xref:core/miscellaneous/configuring-dbcontext)」。
* この例では、`OnConfiguring` のオーバーライドを使用して、Sqlite データ ファイルを指定します。
* `UseLazyLoadingProxies` の呼び出しにより、遅延読み込みを実装するよう EF Core に指示されるため、子エンティティは親からアクセスされた時点で自動的に読み込まれます。

**Ctrl + Shift + B** キーを押すか、 **[ビルド] &gt; [ソリューションのビルド]** に移動して、プロジェクトをコンパイルします。

> [!TIP]
> データベースと EF Core モデルの同期を維持するためのさまざまな方法について確認します: 「[データベース スキーマを管理する](xref:core/managing-schemas/index)」。

## <a name="lazy-loading"></a>遅延読み込み

**Category** クラスの **Products** プロパティと、**Product** クラスの **Category** プロパティは、ナビゲーション プロパティです。 Entity Framework Core では、ナビゲーション プロパティによって、2 つのエンティティ型間のリレーションシップをナビゲートする手段が提供されます。

EF Core を使用すると、ナビゲーション プロパティに初めてアクセスしたときに、関連エンティティをデータベースから自動的に読み込むことができます。 この種類の読み込み (遅延読み込みと呼ばれます) では、各ナビゲーション プロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して別のクエリが実行されることに注意してください。

"単純な従来の C# オブジェクト" (POCO) エンティティ型を使用すると、EF Core では、実行中に派生プロキシ型のインスタンスを作成してから、クラス内の仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みが実現されます。 関連オブジェクトの遅延読み込みを行うには、ナビゲーション プロパティ ゲッターを **public** および **virtual** (Visual Basic では **Overridable**) として宣言する必要があり、クラスを **sealed** (Visual Basic では **NotOverridable**) にしないようにする必要があります。 Database First を使用すると、遅延読み込みを有効にするために、ナビゲーション プロパティは自動的に virtual にされます。 

## <a name="bind-object-to-controls"></a>オブジェクトをコントロールにバインドする

モデルで定義されているクラスを、この WPF アプリケーションのデータ ソースとして追加します。

1. ソリューション エクスプローラーで **MainWindow.xaml** をダブルクリックして、メイン フォームを開きます
1. **[XAML]** タブを選択して、XAML を編集します。
1. 開始 `Window` タグの直後に次のソースを追加して、EF Core エンティティに接続します。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. これにより、"親" カテゴリのソースと、"詳細" 製品の 2 番目のソースが設定されます。
1. 次に、XAML の終了 `Window.Resources` タグの後に、次のマークアップを追加します。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. `CategoryId` はデータベースによって割り当てられ、変更できないので、`ReadOnly` に設定されていることに注意してください。

## <a name="adding-a-details-grid"></a>詳細グリッドの追加

カテゴリを表示するグリッドができたので、製品を表示するための詳細グリッドを追加できます。

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

最後に、`Save` ボタンを追加して、`Button_Click` にクリック イベントを結び付けます。

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

デザイン ビューは次のようになります。

![WPF デザイナーのスクリーンショット](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>データのやり取りを処理するコードを追加する

次に、いくつかのイベント ハンドラーをメイン ウィンドウに追加します。

1. XAML ウィンドウで、 **&lt;Window&gt;** 要素をクリックして、メイン ウィンドウを選択します。
1. **[プロパティ]** ウィンドウで右上の **[イベント]** をクリックし、 **[Loaded]** ラベルの右にあるテキスト ボックスをダブルクリックします。

    ![メイン ウィンドウのプロパティ](_static/wpf-tutorial-loaded.jpg)

これにより、フォームのコード ビハインドが表示されます。`ProductContext` を使用してデータ アクセスを実行するように、コードを編集します。 次に示すようにコードを更新します。

このコードでは、`ProductContext` の実行時間の長いインスタンスが宣言されています。 データをクエリしてデータベースに保存するには、`ProductContext` オブジェクトを使用します。 その後、`ProductContext` インスタンスの `Dispose()` メソッドを、オーバーライドされた `OnClosing` メソッドから呼び出します。コードのコメントで各ステップの動作内容が説明されています。

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> このコードでは、`EnsureCreated()` の呼び出しを使用して、最初の実行時にデータベースを構築します。 これはデモでは許されますが、運用アプリでは、[移行](xref:core/managing-schemas/migrations/index)を調べてスキーマを管理する必要があります。 また、このコードは、ローカルの SQLite データベースを使用しているため、同期的に実行されます。 リモート サーバーが含まれるのが一般的である運用シナリオでは、`Load` および `SaveChanges` メソッドの非同期バージョンを使用することを検討します。

## <a name="test-the-wpf-application"></a>WPF アプリケーションをテストする

**F5** キーを押すか、 **[デバッグ] &gt; [デバッグの開始]** を選択し、アプリケーションをコンパイルして実行します。 `products.db` という名前のファイルでデータベースが自動的に作成されるはずです。 カテゴリ名を入力して Enter キーを押し、下のグリッドに製品を追加します。 [保存] をクリックし、データベースで指定された ID でグリッドが更新されるのを確認します。 行を強調表示にして **Delete** キーを押し、行を削除します。 **[保存]** をクリックすると、エンティティが削除されます。

![実行中のアプリケーション](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>プロパティの変更通知

この例では、4 つのステップでエンティティと UI を同期させています。

1. 最初の `_context.Categories.Load()` の呼び出しで、カテゴリ データが読み込まれます。
1. 遅延読み込みプロキシにより、依存製品データが読み込まれます。
1. `_context.SaveChanges()` を呼び出すと、EF Core に組み込まれている変更追跡により、挿入や削除など、エンティティに対して必要な変更が行われます。
1. `DataGridView.Items.Refresh()` を呼び出すと、新しく生成された ID で強制的に再読み込みが行われます。

これは、この作業開始サンプルには使用できますが、他のシナリオでは追加のコードが必要になる場合があります。 WPF コントロールにより、エンティティのフィールドとプロパティが読み取られて、UI がレンダリングされます。 ユーザー インターフェイス (UI) で値を編集すると、その値がエンティティに渡されます。 データベースから読み込むなどして、エンティティのプロパティの値を直接変更すると、WPF によって変更が UI にすぐには反映されません。 レンダリング エンジンに変更が通知される必要があります。 このプロジェクトでは、手動で `Refresh()` を呼び出すことによってこれを行いました。 この通知を自動化する簡単な方法は、[INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) インターフェイスを実装することです。 WPF のコンポーネントにより、自動的にインターフェイスが検出されて、変更イベントに登録されます。 エンティティでは、これらのイベントを発生させる必要があります。

> [!TIP]
> 変更の処理方法の詳細については、以下を参照してください: [プロパティ変更通知の実装方法](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)に関するページ。

## <a name="next-steps"></a>次の手順

[DbContext の構成](xref:core/miscellaneous/configuring-dbcontext)の詳細について確認します。
