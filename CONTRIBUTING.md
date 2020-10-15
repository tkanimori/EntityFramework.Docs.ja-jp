# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="a7915-101">Entity Framework ドキュメントへの投稿</span><span class="sxs-lookup"><span data-stu-id="a7915-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="a7915-102">記事やコード サンプルを Entity Framework ドキュメントに投稿するプロセスを以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="a7915-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="a7915-103">投稿には、誤字の修正のような簡単なものから、新しい記事のような複雑なものまであります。</span><span class="sxs-lookup"><span data-stu-id="a7915-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="a7915-104">簡単な修正や提案を行う方法</span><span class="sxs-lookup"><span data-stu-id="a7915-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="a7915-105">記事は、マークダウン ファイルとしてリポジトリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="a7915-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="a7915-106">マークダウン ファイルのコンテンツに対して簡単な変更を行うには、ブラウザー ウィンドウの右上隅にある **[編集]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a7915-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="a7915-107">場合によっては、**オプション** バーを展開して、**[編集]** リンクを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7915-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="a7915-108">指示に従って pull request (PR) を作成します。</span><span class="sxs-lookup"><span data-stu-id="a7915-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="a7915-109">EF チームによって、PR がレビューされ、受け入れられるか変更が提案されます。</span><span class="sxs-lookup"><span data-stu-id="a7915-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="a7915-110">もっと複雑な投稿を行う方法</span><span class="sxs-lookup"><span data-stu-id="a7915-110">How to make a more complex submission</span></span>

<span data-ttu-id="a7915-111">[Git と GitHub.com](https://guides.github.com/activities/hello-world/) の基本的な理解が必要です。</span><span class="sxs-lookup"><span data-stu-id="a7915-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="a7915-112">既存の記事の変更や新しい記事の作成など、行いたい内容を説明する[問題](https://github.com/dotnet/EntityFramework.Docs/issues/new)を開きます。</span><span class="sxs-lookup"><span data-stu-id="a7915-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="a7915-113">大量の時間を費やす前に、EF チームの承認を待ちます。</span><span class="sxs-lookup"><span data-stu-id="a7915-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="a7915-114">[dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) リポジトリをフォークして、ご自分の変更用のブランチを作成します。</span><span class="sxs-lookup"><span data-stu-id="a7915-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="a7915-115">変更内容を記載した pull request (PR) をマスターに送信します。</span><span class="sxs-lookup"><span data-stu-id="a7915-115">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="a7915-116">PR のフィードバックに対応します。</span><span class="sxs-lookup"><span data-stu-id="a7915-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="a7915-117">マークダウンの構文</span><span class="sxs-lookup"><span data-stu-id="a7915-117">Markdown syntax</span></span>

<span data-ttu-id="a7915-118">記事は [DocFX Flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) で書かれています。これは [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/) のスーパーセットです。</span><span class="sxs-lookup"><span data-stu-id="a7915-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="a7915-119">EF ドキュメントでよく使用される UI 機能についての DFM 構文およびメタデータの例については、.NET Core リポジトリ スタイル ガイドの「[Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md)」(メタデータおよび Markdown テンプレート) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a7915-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="a7915-120">フォルダー構造の規則</span><span class="sxs-lookup"><span data-stu-id="a7915-120">Folder structure conventions</span></span>

<span data-ttu-id="a7915-121">イメージやその他の静的コンテンツは、サイトの各領域/フォルダー内の `_static` フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="a7915-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="a7915-122">コード サンプルは、`samples`ルート フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="a7915-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="a7915-123">それらは、ドキュメント構造を模倣したフォルダー構造 (`entity-framework`ルート フォルダー下) に編成されます。</span><span class="sxs-lookup"><span data-stu-id="a7915-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="a7915-124">コード スニペット</span><span class="sxs-lookup"><span data-stu-id="a7915-124">Code snippets</span></span>

<span data-ttu-id="a7915-125">記事には、ポイントを説明するためのコード スニペットが含まれることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="a7915-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="a7915-126">DFM では、マークダウン ファイルにコードをコピーしたり、個別のコード ファイルを参照したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="a7915-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="a7915-127">コードのエラーの可能性を最小限にするため、可能な限り個別のコード ファイルを使用してください。</span><span class="sxs-lookup"><span data-stu-id="a7915-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="a7915-128">コード ファイルは、先述のサンプル プロジェクトのフォルダー構造を使用してリポジトリに格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a7915-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="a7915-129">[DFM コード スニペットの構文](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a7915-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="a7915-130">コード ファイル全体をスニペットとして表示するには:</span><span class="sxs-lookup"><span data-stu-id="a7915-130">To render an entire code file as a snippet:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="a7915-131">行番号を使用してファイルの一部をスニペットとしてレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="a7915-131">To render a portion of a file as a snippet by using line numbers:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="a7915-132">C# スニペットの場合は、[C# の領域](https://msdn.microsoft.com/library/9a1ybwek.aspx)を参照できます。</span><span class="sxs-lookup"><span data-stu-id="a7915-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="a7915-133">行番号ではなく領域を使用してください。</span><span class="sxs-lookup"><span data-stu-id="a7915-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="a7915-134">コード ファイル内の行番号は変更されて、Markdown での行番号参照と同期しなくなることがよくあるためです。</span><span class="sxs-lookup"><span data-stu-id="a7915-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="a7915-135">C# の領域は入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a7915-135">C# regions can be nested.</span></span> <span data-ttu-id="a7915-136">外側の領域を参照した場合、内側の `#region` と `#endregion` ディレクティブはスニペットにレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="a7915-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="a7915-137">"snippet_Example" という名前の C# 領域をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="a7915-137">To render a C# region named "snippet_Example":</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="a7915-138">表示されたスニペットで選択されている行を強調表示するには (通常は黄色の背景色で表示されます):</span><span class="sxs-lookup"><span data-stu-id="a7915-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="a7915-139">DocFX で変更をテストする</span><span class="sxs-lookup"><span data-stu-id="a7915-139">Test your changes with DocFX</span></span>

<span data-ttu-id="a7915-140">[DocFX コマンド ライン ツール](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)を使用して変更をテストします。このツールは、ローカルにホストされたバージョンのサイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a7915-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="a7915-141">DocFX では、docs.microsoft.com 用に作成されたスタイルやサイトの拡張機能は表示されません。</span><span class="sxs-lookup"><span data-stu-id="a7915-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="a7915-142">DocFX には、Windows または、Linux または macOS 用の Mono 上の .NET Framework が必要です。</span><span class="sxs-lookup"><span data-stu-id="a7915-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="a7915-143">Windows での手順</span><span class="sxs-lookup"><span data-stu-id="a7915-143">Windows instructions</span></span>

* <span data-ttu-id="a7915-144">[DocFX リリース](https://github.com/dotnet/docfx/releases)から *docfx.zip* をダウンロードして解凍します。</span><span class="sxs-lookup"><span data-stu-id="a7915-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="a7915-145">DocFX を PATH に追加します。</span><span class="sxs-lookup"><span data-stu-id="a7915-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="a7915-146">コマンド ライン ウィンドウで、複製されたリポジトリ (ここには *docfx.json* ファイルが格納される) に移動し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a7915-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ```console
   docfx -t default --serve
   ```

* <span data-ttu-id="a7915-147">ブラウザーで、`http://localhost:8080` に移動します。</span><span class="sxs-lookup"><span data-stu-id="a7915-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="a7915-148">Mono での手順</span><span class="sxs-lookup"><span data-stu-id="a7915-148">Mono instructions</span></span>

* <span data-ttu-id="a7915-149">Homebrew を使用して Mono をインストールします (`brew install mono`)。</span><span class="sxs-lookup"><span data-stu-id="a7915-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="a7915-150">[最新バージョンの DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2) をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="a7915-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="a7915-151">`\bin\docfx` に展開します。</span><span class="sxs-lookup"><span data-stu-id="a7915-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="a7915-152">**docfx** の別名を作成します。</span><span class="sxs-lookup"><span data-stu-id="a7915-152">Create an alias for **docfx**:</span></span>

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="a7915-153">複製されたリポジトリ内の **docfx** を実行して、サイトを構築し、**docfx serve** を実行して、`http://localhost:8080` でサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="a7915-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="a7915-154">ボイスとトーン</span><span class="sxs-lookup"><span data-stu-id="a7915-154">Voice and tone</span></span>

<span data-ttu-id="a7915-155">目標は、できるかぎり幅広いユーザーにわかりやすいドキュメントを作成することです。</span><span class="sxs-lookup"><span data-stu-id="a7915-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="a7915-156">そのため、共同作成者に従っていただきたい文書のスタイルのガイドラインが設けられています。</span><span class="sxs-lookup"><span data-stu-id="a7915-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="a7915-157">詳細については、.NET Core リポジトリの「[Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md)」(スタイルとトーンのガイドライン) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a7915-157">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
