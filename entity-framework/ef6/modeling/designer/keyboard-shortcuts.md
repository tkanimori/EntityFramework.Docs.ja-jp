---
title: Entity Framework デザイナーのキーボード ショートカット - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
caps.latest.revision: 3
ms.openlocfilehash: a4ed95647872949d9e34a6bb5d83d5d6119b0022
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122819"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework デザイナーのキーボード ショートカット
このページは、Entity Framework Tools for Visual Studio のさまざまな画面で利用できるキーボード ショートカットのリンクの一覧を示します。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET Entity Data Model ウィザード

### <a name="step-one-choose-model-contents"></a>手順 1: モデル コンテンツを選択します。

![WizardOne](~/ef6/media/wizardone.png)

| ショートカット  | アクション                                                     | メモ                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 次の画面に移動します。                                        | モデルのコンテンツのすべての選択は使用できません。 |
| **Alt + f** | ウィザードを完了します。                                              | モデルのコンテンツのすべての選択は使用できません。 |
| **Alt + w** | 「どのようなモデルに含めるか?」にフォーカスを切り替える ウィンドウです。 |                                                     |

### <a name="step-two-choose-your-connection"></a>手順 2: 接続を選択します。

![WizardTwo](~/ef6/media/wizardtwo.png)

| ショートカット  | アクション                                                     | メモ                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 次の画面に移動します。                                        |                                                         |
| **Alt + p** | 前の画面に移動します。                                    |                                                         |
| **Alt + w** | 「どのようなモデルに含めるか?」にフォーカスを切り替える ウィンドウです。 |                                                         |
| **Alt + c** | [接続プロパティ] ウィンドウを開く                    | 新しいデータベース接続の定義をできます。 |
| **Alt + e** | 接続文字列から機密データを除外します。          |                                                         |
| **Alt + i** | 接続文字列に機密データを含める            |                                                         |
| **Alt + s** | "App.Config で接続の設定を保存する オプションを切り替える |                                                         |

### <a name="step-three-choose-your-version"></a>手順 3: バージョンを選択します。

![WizardThree](~/ef6/media/wizardthree.png)

| ショートカット  | アクション                                             | メモ                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 次の画面に移動します。                                |                                                                                       |
| **Alt + p** | 前の画面に移動します。                            |                                                                                       |
| **Alt + w** | Entity Framework のバージョンの選択にフォーカスを切り替える | プロジェクトで使用するための別のバージョンの Entity Framework を指定できるようにします。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>手順 4: データベース オブジェクトと設定を選択します。

![WizardFour](~/ef6/media/wizardfour.png)

| ショートカット  | アクション                                                                                    | メモ                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | ウィザードを完了します。                                                                             |                                                                     |
| **Alt + p** | 前の画面に移動します。                                                                   |                                                                     |
| **Alt + w** | データベース オブジェクトの選択 ウィンドウにフォーカスを切り替える                                            | 反転するデータベース オブジェクトを指定するエンジニア リングをできるようにします。    |
| **Alt + s** | 切り替え、"化または単数生成されたオブジェクト名"オプション                       |                                                                     |
| **Alt + k** | 「モデルに外部キー列を含める」オプションを切り替える                              | モデルのコンテンツのすべての選択は使用できません。                 |
| **Alt + i** | 「選択されたインポート ストアド プロシージャおよび関数、エンティティ モデルに」オプションを切り替える | モデルのコンテンツのすべての選択は使用できません。                 |
| **Alt + m** | "モデル Namespace"のテキスト フィールドにフォーカスを移動します。                                        | モデルのコンテンツのすべての選択は使用できません。                 |
| **スペース** | 要素の選択範囲の切り替え                                                               | 要素に子がある場合は、すべての子要素が切り替わりますも |
| **左**  | 子ツリーを折りたたむ                                                                       |                                                                     |
| **右** | 子ツリーを展開します。                                                                         |                                                                     |
| **アップ**    | ツリー内の直前の要素に移動します                                                      |                                                                     |
| **ダウン**  | ツリーの次の要素に移動します                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF デザイナー画面

![DesignerSurface](~/ef6/media/designersurface.png)

| ショートカット                                                                                | アクション                      | メモ                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **スペースまたは入力します**                                                                         | 選択範囲の切り替え            | フォーカスを持つオブジェクトの選択を切り替えます。                                                                                                                                                                                         |
| **Esc**                                                                                 | 選択を解除します。            | 現在の選択を取り消します。                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | すべて選択                  | デザイン サーフェイス上のすべての図形を選択します。                                                                                                                                                                                       |
| **上矢印**                                                                            | 上へ移動                     | 選択した 1 つのグリッドの増分値をエンティティに移動します。 <br/> リストの場合は、前の兄弟サブフィールドに移動します。                                                                                                                            |
| **下向きの矢印**                                                                          | 下へ移動                   | 選択した 1 つのグリッドの増分値をエンティティに移動します。 <br/> リストの場合は、次の兄弟サブフィールドに移動します。                                                                                                                              |
| **← キー**                                                                          | 左に移動                   | 選択した左側の 1 つのグリッド インクリメントをエンティティに移動します。 <br/> リストの場合は、前の兄弟サブフィールドに移動します。                                                                                                                          |
| **→ キー**                                                                         | 右に移動します。                  | 選択したエンティティに右のグリッドのインクリメントに移動します。 <br/> リストの場合は、次の兄弟サブフィールドに移動します。                                                                                                                             |
| **Shift キーを押しながら左方向キー**                                                                  | サイズの図形を左             | 増分値の 1 つのグリッドで選択したエンティティの幅が減少します。                                                                                                                                                                     |
| **Shift キーを押しながら右矢印**                                                                 | 適切なサイズの図形            | 増分値の 1 つのグリッドで選択したエンティティの幅が向上します。                                                                                                                                                                   |
| **Home**                                                                                | 最初のピア                  | フォーカスを移動して、同じピア レベルのデザイン画面の最初のオブジェクトを選択します。                                                                                                                                         |
| **End**                                                                                 | 最後のピア                   | フォーカスを移動して、同じピア レベルのデザイン画面の最後のオブジェクトを選択します。                                                                                                                                          |
| **Ctrl キーを押しながらホーム**                                                                         | 最初のピア (フォーカス設定)          | 最初のピアがフォーカスを移動するフォーカスと選択を移動する代わりと同じです。                                                                                                                                                          |
| **Ctrl キーを押しながら End**                                                                          | 最後のピア (フォーカス設定)           | 前回と同じ、ピアが、フォーカスと選択を移動する代わりにフォーカスを移動します。                                                                                                                                                           |
| **Tab**                                                                                 | 次のピア                   | フォーカスを移動して、同じピア レベルのデザイン画面で次のオブジェクトを選択します。                                                                                                                                          |
| **Shift + Tab**                                                                           | 前のピア               | フォーカスを移動して、同じピア レベルのデザイン画面で、前のオブジェクトを選択します。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 次のピア (フォーカス設定)           | 次と同じでは、ピアが、フォーカスと選択を移動する代わりにフォーカスを移動します。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 前のピア (フォーカス設定)       | 前のピアがフォーカスを移動するフォーカスと選択を移動する代わりと同じです。                                                                                                                                                       |
| **&lt;**                                                                                | Ascend                      | デザイン サーフェス 1 つのレベル、階層内の上位の次のオブジェクトに移動します。 階層内には、この図形の上の図形がないかどうか (つまり、オブジェクトがデザイン サーフェイスで直接)、配置の図を選択します。 |
| **&gt;**                                                                                | 降下                     | この階層の下のデザイン画面 1 つのレベルで、[次へ] の含まれるオブジェクトに移動します。 含まれるオブジェクトがない場合は、no-op になります。                                                                              |
| **Ctrl + &lt;**                                                                         | Ascend (フォーカス設定)              | フォーカスを移動する選択なしのコマンドを ascend と同じです。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | 降下 (フォーカス設定)             | フォーカスを移動する選択なしのコマンドを降下と同じです。                                                                                                                                                                         |
| **Shift キーを押しながら End**                                                                         | 次の接続         | エンティティからこのエンティティに接続されているエンティティに移動します。                                                                                                                                                               |
| **Del**                                                                                 | 削除                      | オブジェクトまたはコネクタをダイアグラムから削除します。                                                                                                                                                                                     |
| **アドイン**                                                                                 | 挿入                      | 「スカラー プロパティ」コンパートメントのヘッダーまたはプロパティ自体のいずれかを選択すると、エンティティに新しいプロパティを追加します。                                                                                                           |
| **前のページ**                                                                               | スクロール ダイアグラム           | 現在表示されているデザイン画面の高さの 75% に等しい単位で、デザイン画面をスクロールします。                                                                                                                    |
| **次のページ**                                                                             | 下へスクロール ダイアグラム         | デザイン画面をスクロール ダウンします。                                                                                                                                                                                                    |
| **Shift キーを押しながら次のページ**                                                                     | 右側のスクロール ダイアグラム        | デザイン サーフェイスを右にスクロールします。                                                                                                                                                                                            |
| **Shift + 前のページ**                                                                       | 左スクロール ダイアグラム         | デザイン サーフェイスを左にスクロールします。                                                                                                                                                                                             |
| **F2**                                                                                  | 編集モードします。             | テキスト コントロールの編集モードを入力するための標準のキーボード ショートカット。                                                                                                                                                               |
| **Shift + F10**                                                                         | ショートカット メニューを表示します。       | 選択した項目のショートカット メニューを表示するための標準のキーボード ショートカット。                                                                                                                                                          |
| **コントロール + Shift + マウスの左クリックします。**  <br/> **コントロール + Shift + マウス ホイール前方**  | セマンティック ズーム イン            | マウス ポインターの下にあるダイアグラム ビューの領域にズーム インします。                                                                                                                                                                 |
| **コントロール + Shift + マウスを右クリックします。** <br/> **コントロール + Shift + マウス ホイール後方** | セマンティック ズーム アウト           | マウス ポインターの下にあるダイアグラム ビューの領域を縮小します。 離れすぎての現在のダイアグラム センターの縮小すると、図は、再センターします。                                                                          |
| **制御 + Shift + '+'** <br/> **コントロール + マウス ホイール前方**                        | 拡大                     | ダイアグラム ビューの中心にズーム インします。                                                                                                                                                                                         |
| **制御 + Shift + '-'** <br/> **コントロール + マウス ホイール後方**                       | 縮小                    | ダイアグラム ビューのクリックされた領域を縮小します。 離れすぎての現在のダイアグラム センターの縮小すると、図は、再センターします。                                                                                            |
| **制御 + Shift + マウスの左ボタンの四角形を描画**                  | ズーム領域                   | 選択した領域の中央にズームインします。 コントロール + Shift キーを押し、虫眼鏡にズーム インする領域を定義することができますにカーソルが変わることがわかります。                        |
| **コンテキスト メニュー キー + います '**                                                              | [マッピングの詳細] ウィンドウを開く | 選択したエンティティのマッピングを編集するマッピングの詳細ウィンドウを開きます                                                                                                                                                               |

## <a name="mapping-details-window"></a>[マッピングの詳細] ウィンドウ

![MappingDetailsShortcuts](~/ef6/media/mappingdetailsshortcuts.png)

| ショートカット                  | アクション         | メモ                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Tab**                   | コンテキストの切り替え | メイン ウィンドウ領域と、ツールバーの左側を切り替えます                                                                     |
| **方向キー**            | ナビゲーション     | メイン ウィンドウ領域内の列の間で、行、または左右上下移動します。 左側のツールバーのボタン間を移動します。 |
| **Enter** <br/> **スペース** | 選択         | 左側のツールバーでボタンを選択します。                                                                                          |
| **Alt + 下方向キー**      | 一覧を開きます      | 一覧がドロップダウンをドロップダウン リストを持つセルが選択されている場合。                                                                     |
| **Enter**                 | リストの選択    | ドロップダウン リストで要素を選択します。                                                                                               |
| **Esc**                   | ボックスの一覧を閉じる     | ドロップダウン リストを閉じます。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio のナビゲーション

Entity Framework では、さまざまなカスタムのキーボード ショートカットをマップできるアクションも用意されて (ショートカットない既定でマッピングされます)。 これらのカスタム ショートカットを作成するには、ツール メニューの オプションをクリックします。  [環境] の [キーボード] を選択します。  目的のコマンドを選択、「のショートカット キーを押して」テキスト ボックスで、ショートカットを入力し、クリックしたことができますになるまでの中央の一覧をスクロールして割り当てます。 可能なショートカットは次のとおりです。

| ショートカット                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Add.ComplexProperty.ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddFunctionImport**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.AddEnumType**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Association**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ComplexProperty**                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ComplexType**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Entity**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.FunctionImport**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.Inheritance**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.NavigationProperty**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNew.ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Close**                                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Collapse**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.CollapseAll**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.ExpandAll**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.ExportasImage**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Diagram.LayoutDiagram**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Edit**                                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.EntityKey**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Expand**                                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GoToDefinition**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Grid.ShowGrid**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Grid.SnaptoGrid**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ModelBrowser**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Open**                                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Rename**                                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayName**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.BaseType**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Entity**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Property**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Select.Subtype**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Validate**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.10**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.100**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.125**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.150**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.200**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.25**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.300**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.33**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.400**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.50**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.66**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.75**                                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.Custom**                             |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomIn**                             |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomOut**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Zoom.ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
