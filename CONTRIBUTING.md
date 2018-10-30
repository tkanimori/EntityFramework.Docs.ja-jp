# <a name="contributing-to-the-entity-framework-documentation"></a>Entity Framework ドキュメントへの投稿

このドキュメントでは、[Entity Framework ドキュメント サイト](https://docs.microsoft.com/ef)でホストされている記事とコード サンプルに投稿するためのプロセスを説明します。 投稿には、誤字の修正のような簡単なものから、新しい記事のような複雑なものまであります。

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>簡単な修正や提案を行う方法

記事は、マークダウン ファイルとしてリポジトリに格納されます。 マークダウン ファイルのコンテンツに対する簡単な変更は、ブラウザーでブラウザー ウィンドウの右上隅にある **[編集]** リンクをタップして行います  (幅の狭いブラウザー ウィンドウでは、**オプション** バーを展開して、**[編集]** リンクを表示する必要がある場合があります)。指示に従ってプル要求 (PR) を作成します。 EF チームによって、PR がレビューされ、受け入れられるか変更が提案されます。

## <a name="how-to-make-a-more-complex-submission"></a>もっと複雑な投稿を行う方法

[Git と GitHub.com](https://guides.github.com/activities/hello-world/) の基本的な理解が必要です。

* 既存の記事の変更や新しい記事の作成など、行いたい内容を説明する[問題](https://github.com/aspnet/EntityFramework.Docs/issues/new)を開きます。 大量の時間を費やす前に、EF チームの承認を待ちます。
* [aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) リポジトリをフォークして、ご自分の変更用のブランチを作成します。
* 変更内容を記載したプル要求 (PR) をマスターに送信します。
* PR のフィードバックに対応します。

## <a name="markdown-syntax"></a>マークダウンの構文

記事は [DocFx-flavored Markdown](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) で書かれており、これは [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/) のスーパーセットです。 EF ドキュメントでよく使用される UI 機能についての DFM 構文の例については、.NET Core リポジトリ スタイル ガイドの「[Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md)」(メタデータおよびマークダウン テンプレート) を参照してください。 

## <a name="folder-structure-conventions"></a>フォルダー構造の規則

イメージやその他の静的コンテンツは、サイトの各領域/フォルダー内の `_static` フォルダーに格納されます。

コード サンプルは、`samples`ルート フォルダーに格納されます。 それらは、ドキュメント構造を模倣したフォルダー構造 (`entity-framework`ルート フォルダー下) に編成されます。

## <a name="code-snippets"></a>コード スニペット

記事には、ポイントを説明するためのコード スニペットが含まれることがよくあります。 DFM では、マークダウン ファイルにコードをコピーしたり、個別のコード ファイルを参照したりすることができます。 コードのエラーの可能性を最小限にするため、可能な限り個別のコード ファイルを使用することをお勧めします。 コード ファイルは、先述のサンプル プロジェクトのフォルダー構造を使用してリポジトリに格納する必要があります。

[DFM コード スニペットの構文](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)の例を次に示します。

コード ファイル全体をスニペットとしてレンダリングするには:

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

行番号を使用してファイルの一部をスニペットとしてレンダリングするには:

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

C# スニペットの場合は、[C# の領域](https://msdn.microsoft.com/library/9a1ybwek.aspx)を参照できます。 可能な場合は常に、行番号ではなく領域を使用してください。コード ファイル内の行番号は変更されて、Markdown での行番号参照と同期しなくなることがよくあるためです。 C# の領域は入れ子にすることができ、外側の領域を参照した場合、内側の `#region` と `#endregion` ディレクティブはスニペットにレンダリングされません。

"snippet_Example" という名前の C# 領域をレンダリングするには:

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

レンダリングされたスニペットで選択されている行を強調表示するには (通常は黄色の背景色でレンダリングされます):

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>DocFX で変更をテストする

[DocFX コマンド ライン ツール](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)を使用して変更をテストします。このツールは、ローカルにホストされるバージョンのサイトを作成します。 DocFX では、docs.microsoft.com 用に作成されたスタイルやサイトの拡張機能はレンダリングされません。

DocFX には、Windows または、Linux または macOS 用の Mono 上の .NET Framework が必要です。

### <a name="windows-instructions"></a>Windows での手順

* [DocFX リリース](https://github.com/dotnet/docfx/releases)から *docfx.zip* をダウンロードして解凍します。
* DocFX を PATH に追加します。
* コマンド ライン ウィンドウで、複製されたリポジトリ (ここには *docfx.json* ファイルが格納される) に移動し、次のコマンドを実行します。

   ``` console
   docfx -t default --serve
   ```

* ブラウザーで、`http://localhost:8080` に移動します。

### <a name="mono-instructions"></a>Mono での手順

* Homebrew を使用して Mono をインストールします (`brew install mono`)。
* [最新バージョンの DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2) をダウンロードします。
* `\bin\docfx` に展開します。
* **docfx** の別名を作成します。

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* 複製されたリポジトリ内の **docfx** を実行して、サイトを構築し、**docfx serve** を実行して、`http://localhost:8080` でサイトを表示します。

## <a name="voice-and-tone"></a>スタイルとトーン

目標は、できるかぎり幅広いユーザーにわかりやすいドキュメントを作成することです。 そのため、共同作成者に従っていただきたい文書のスタイルのガイドラインが設けられています。 詳細については、.NET Core リポジトリの「[Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md)」(スタイルとトーンのガイドライン) を参照してください。
