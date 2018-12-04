# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="ca0e3-101">Entity Framework ドキュメントへの投稿</span><span class="sxs-lookup"><span data-stu-id="ca0e3-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="ca0e3-102">このドキュメントでは、[Entity Framework ドキュメント サイト](https://docs.microsoft.com/ef)でホストされている記事とコード サンプルに投稿するためのプロセスを説明します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-102">The document covers the process for contributing to the articles and code samples that are hosted on the [Entity Framework documentation site](https://docs.microsoft.com/ef).</span></span> <span data-ttu-id="ca0e3-103">投稿には、誤字の修正のような簡単なものから、新しい記事のような複雑なものまであります。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-103">Contributions may be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="ca0e3-104">簡単な修正や提案を行う方法</span><span class="sxs-lookup"><span data-stu-id="ca0e3-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="ca0e3-105">記事は、マークダウン ファイルとしてリポジトリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="ca0e3-106">マークダウン ファイルのコンテンツに対する簡単な変更は、ブラウザーでブラウザー ウィンドウの右上隅にある **[編集]** リンクをタップして行います </span><span class="sxs-lookup"><span data-stu-id="ca0e3-106">Simple changes to the content of a Markdown file can be made in the browser by tapping the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="ca0e3-107">(幅の狭いブラウザー ウィンドウでは、**オプション** バーを展開して、**[編集]** リンクを表示する必要がある場合があります)。指示に従って pull request (PR) を作成します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-107">(In narrow browser windows you might need to expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="ca0e3-108">EF チームによって、PR がレビューされ、受け入れられるか変更が提案されます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-108">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="ca0e3-109">もっと複雑な投稿を行う方法</span><span class="sxs-lookup"><span data-stu-id="ca0e3-109">How to make a more complex submission</span></span>

<span data-ttu-id="ca0e3-110">[Git と GitHub.com](https://guides.github.com/activities/hello-world/) の基本的な理解が必要です。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-110">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="ca0e3-111">既存の記事の変更や新しい記事の作成など、行いたい内容を説明する[問題](https://github.com/aspnet/EntityFramework.Docs/issues/new)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-111">Open an [issue](https://github.com/aspnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="ca0e3-112">大量の時間を費やす前に、EF チームの承認を待ちます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-112">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="ca0e3-113">[aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) リポジトリをフォークして、ご自分の変更用のブランチを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-113">Fork the [aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="ca0e3-114">変更内容を記載した pull request (PR) をマスターに送信します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-114">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="ca0e3-115">PR のフィードバックに対応します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-115">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="ca0e3-116">マークダウンの構文</span><span class="sxs-lookup"><span data-stu-id="ca0e3-116">Markdown syntax</span></span>

<span data-ttu-id="ca0e3-117">記事は [DocFx-flavored Markdown](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) で書かれており、これは [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/) のスーパーセットです。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-117">Articles are written in [DocFx-flavored Markdown](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="ca0e3-118">EF ドキュメントでよく使用される UI 機能についての DFM 構文の例については、.NET Core リポジトリ スタイル ガイドの「[Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md)」(メタデータおよびマークダウン テンプレート) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-118">For examples of DFM syntax for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="ca0e3-119">フォルダー構造の規則</span><span class="sxs-lookup"><span data-stu-id="ca0e3-119">Folder structure conventions</span></span>

<span data-ttu-id="ca0e3-120">イメージやその他の静的コンテンツは、サイトの各領域/フォルダー内の `_static` フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-120">Images, and other static content, are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="ca0e3-121">コード サンプルは、`samples`ルート フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-121">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="ca0e3-122">それらは、ドキュメント構造を模倣したフォルダー構造 (`entity-framework`ルート フォルダー下) に編成されます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-122">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="ca0e3-123">コード スニペット</span><span class="sxs-lookup"><span data-stu-id="ca0e3-123">Code snippets</span></span>

<span data-ttu-id="ca0e3-124">記事には、ポイントを説明するためのコード スニペットが含まれることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-124">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="ca0e3-125">DFM では、マークダウン ファイルにコードをコピーしたり、個別のコード ファイルを参照したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-125">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="ca0e3-126">コードのエラーの可能性を最小限にするため、可能な限り個別のコード ファイルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-126">We prefer to use separate code files whenever possible, to minimize the chance of errors in the code.</span></span> <span data-ttu-id="ca0e3-127">コード ファイルは、先述のサンプル プロジェクトのフォルダー構造を使用してリポジトリに格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-127">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="ca0e3-128">[DFM コード スニペットの構文](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-128">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="ca0e3-129">コード ファイル全体をスニペットとしてレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="ca0e3-129">To render an entire code file as a snippet:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="ca0e3-130">行番号を使用してファイルの一部をスニペットとしてレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="ca0e3-130">To render a portion of a file as a snippet by using line numbers:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="ca0e3-131">C# スニペットの場合は、[C# の領域](https://msdn.microsoft.com/library/9a1ybwek.aspx)を参照できます。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-131">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="ca0e3-132">可能な場合は常に、行番号ではなく領域を使用してください。コード ファイル内の行番号は変更されて、Markdown での行番号参照と同期しなくなることがよくあるためです。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-132">Whenever possible, use regions rather than line numbers, because line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="ca0e3-133">C# の領域は入れ子にすることができ、外側の領域を参照した場合、内側の `#region` と `#endregion` ディレクティブはスニペットにレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-133">C# regions can be nested, and if you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="ca0e3-134">"snippet_Example" という名前の C# 領域をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="ca0e3-134">To render a C# region named "snippet_Example":</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="ca0e3-135">レンダリングされたスニペットで選択されている行を強調表示するには (通常は黄色の背景色でレンダリングされます):</span><span class="sxs-lookup"><span data-stu-id="ca0e3-135">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="ca0e3-136">DocFX で変更をテストする</span><span class="sxs-lookup"><span data-stu-id="ca0e3-136">Test your changes with DocFX</span></span>

<span data-ttu-id="ca0e3-137">[DocFX コマンド ライン ツール](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)を使用して変更をテストします。このツールは、ローカルにホストされるバージョンのサイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-137">Test your changes with the [DocFX command line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="ca0e3-138">DocFX では、docs.microsoft.com 用に作成されたスタイルやサイトの拡張機能はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-138">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="ca0e3-139">DocFX には、Windows または、Linux または macOS 用の Mono 上の .NET Framework が必要です。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-139">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="ca0e3-140">Windows での手順</span><span class="sxs-lookup"><span data-stu-id="ca0e3-140">Windows instructions</span></span>

* <span data-ttu-id="ca0e3-141">[DocFX リリース](https://github.com/dotnet/docfx/releases)から *docfx.zip* をダウンロードして解凍します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-141">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="ca0e3-142">DocFX を PATH に追加します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-142">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="ca0e3-143">コマンド ライン ウィンドウで、複製されたリポジトリ (ここには *docfx.json* ファイルが格納される) に移動し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-143">In a command line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ``` console
   docfx -t default --serve
   ```

* <span data-ttu-id="ca0e3-144">ブラウザーで、`http://localhost:8080` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-144">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="ca0e3-145">Mono での手順</span><span class="sxs-lookup"><span data-stu-id="ca0e3-145">Mono instructions</span></span>

* <span data-ttu-id="ca0e3-146">Homebrew を使用して Mono をインストールします (`brew install mono`)。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-146">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="ca0e3-147">[最新バージョンの DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2) をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-147">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="ca0e3-148">`\bin\docfx` に展開します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-148">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="ca0e3-149">**docfx** の別名を作成します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-149">Create an alias for **docfx**:</span></span>

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="ca0e3-150">複製されたリポジトリ内の **docfx** を実行して、サイトを構築し、**docfx serve** を実行して、`http://localhost:8080` でサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-150">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="ca0e3-151">スタイルとトーン</span><span class="sxs-lookup"><span data-stu-id="ca0e3-151">Voice and tone</span></span>

<span data-ttu-id="ca0e3-152">目標は、できるかぎり幅広いユーザーにわかりやすいドキュメントを作成することです。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-152">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="ca0e3-153">そのため、共同作成者に従っていただきたい文書のスタイルのガイドラインが設けられています。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-153">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="ca0e3-154">詳細については、.NET Core リポジトリの「[Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md)」(スタイルとトーンのガイドライン) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca0e3-154">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
