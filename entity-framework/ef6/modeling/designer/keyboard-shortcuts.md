---
title: Entity Framework Designer のキーボードショートカット-EF6
description: Entity Framework 6 の Entity Framework Designer キーボードショートカット
author: divega
ms.date: 10/23/2016
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
ms.openlocfilehash: 8b93581e68e3a5d83685794f034c50cfe74eee9f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620461"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework Designer のキーボードショートカット
このページでは、Visual Studio Entity Framework Tools のさまざまな画面で使用できるキーボード shorcuts の一覧を示します。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET Entity Data Model ウィザード

### <a name="step-one-choose-model-contents"></a>手順 1: モデルの内容を選択する

![ウィザード1](~/ef6/media/wizardone.png)

| ショートカット  | アクション                                                     | Notes                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 次の画面に移動                                        | モデルコンテンツのすべての選択で使用できるわけではありません。 |
| **Alt + f** | ウィザードを終了します。                                              | モデルコンテンツのすべての選択で使用できるわけではありません。 |
| **Alt + w** | 「モデルに含まれる内容」にフォーカスを切り替えます。 枠内. |                                                     |

### <a name="step-two-choose-your-connection"></a>手順 2: 接続を選択する

![ウィザード2](~/ef6/media/wizardtwo.png)

| ショートカット  | アクション                                                     | Notes                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 次の画面に移動                                        |                                                         |
| **Alt + p** | 前の画面に移動                                    |                                                         |
| **Alt + w** | 「モデルに含まれる内容」にフォーカスを切り替えます。 枠内. |                                                         |
| **Alt + c** | [接続プロパティ] ウィンドウを開く                    | 新しいデータベース接続の定義を許可します。 |
| **Alt + e** | 接続文字列から機密データを除外する          |                                                         |
| **Alt + i** | 接続文字列に機微なデータを含める            |                                                         |
| **Alt + s** | [接続設定を App.Config に保存する] オプションをオンにします。 |                                                         |

### <a name="step-three-choose-your-version"></a>手順 3: バージョンを選択する

![ウィザード3](~/ef6/media/wizardthree.png)

| ショートカット  | アクション                                             | Notes                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 次の画面に移動                                |                                                                                       |
| **Alt + p** | 前の画面に移動                            |                                                                                       |
| **Alt + w** | Entity Framework バージョンの選択にフォーカスを切り替える | では、プロジェクトで使用する Entity Framework の別のバージョンを指定できます。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>手順 4: データベースオブジェクトと設定を選択する

![ウィザード4](~/ef6/media/wizardfour.png)

| ショートカット  | アクション                                                                                    | Notes                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | ウィザードを終了します。                                                                             |                                                                     |
| **Alt + p** | 前の画面に移動                                                                   |                                                                     |
| **Alt + w** | データベースオブジェクトの選択ウィンドウにフォーカスを切り替える                                            | リバースエンジニアリングするデータベースオブジェクトを指定できます。    |
| **Alt + s** | [生成されたオブジェクト名を複数化または単数する] オプションを切り替えます。                       |                                                                     |
| **Alt + k** | [モデルに外部キー列を含める] オプションを切り替えます                              | モデルコンテンツのすべての選択で使用できるわけではありません。                 |
| **Alt + i** | [選択したストアドプロシージャと関数をエンティティモデルにインポートする] オプションをオンにします。 | モデルコンテンツのすべての選択で使用できるわけではありません。                 |
| **Alt + m** | "モデル名前空間" テキストフィールドにフォーカスを切り替えます。                                        | モデルコンテンツのすべての選択で使用できるわけではありません。                 |
| **行間** | 要素の選択の切り替え                                                               | 要素に子が含まれている場合は、すべての子要素も同様に切り替わります |
| **Left**  | 子ツリーの折りたたみ                                                                       |                                                                     |
| **Right** | 子ツリーの展開                                                                         |                                                                     |
| **Up**    | ツリー内の前の要素に移動                                                      |                                                                     |
| **[下へ]**  | ツリー内の次の要素に移動                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF デザイナー画面

![デザイナー画面](~/ef6/media/designersurface.png)

| ショートカット                                                                                | アクション                      | Notes                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Space または Enter**                                                                         | 選択範囲の切り替え            | フォーカスがあるオブジェクトの選択を切り替えます。                                                                                                                                                                                         |
| **Esc**                                                                                 | 選択の取り消し            | 現在の選択を取り消します。                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | [すべて選択]                  | デザインサーフェイス上のすべての図形を選択します。                                                                                                                                                                                       |
| **上矢印**                                                                            | 上へ移動                     | 選択したエンティティを1グリッドインクリメントで上に移動します。 <br/> リスト内にある場合、は前の兄弟サブフィールドに移動します。                                                                                                                            |
| **下矢印**                                                                          | 下へ移動                   | 選択したエンティティを1グリッドずつ下に移動します。 <br/> リスト内にある場合、は次の兄弟サブフィールドに移動します。                                                                                                                              |
| **← キー**                                                                          | 左に移動                   | 選択したエンティティを1グリッドずつ左に移動します。 <br/> リスト内にある場合、は前の兄弟サブフィールドに移動します。                                                                                                                          |
| **→ キー**                                                                         | 右に移動                  | 選択したエンティティを1グリッドインクリメントで右に移動します。 <br/> リスト内にある場合、は次の兄弟サブフィールドに移動します。                                                                                                                             |
| **Shift + ←**                                                                  | 図形のサイズを左に変更             | 選択したエンティティの幅を1グリッドインクリメントで減らします。                                                                                                                                                                     |
| **Shift + →**                                                                 | 図形のサイズを右に変更            | 選択したエンティティの幅を1グリッドインクリメントで増やします。                                                                                                                                                                   |
| **ホーム**                                                                                | 最初のピア                  | デザインサーフェイス上の同じピアレベルの最初のオブジェクトにフォーカスと選択を移動します。                                                                                                                                         |
| **End**                                                                                 | 最後のピア                   | デザインサーフェイス上の同じピアレベルの最後のオブジェクトにフォーカスと選択を移動します。                                                                                                                                          |
| **Ctrl + Home**                                                                         | 最初のピア (フォーカス)          | 最初のピアと同じですが、フォーカスを移動して選択するのではなく、フォーカスを移動します。                                                                                                                                                          |
| **Ctrl + End**                                                                          | 最後のピア (フォーカス)           | 最後のピアと同じですが、フォーカスを移動して選択するのではなく、フォーカスを移動します。                                                                                                                                                           |
| **タブ**                                                                                 | 次のピア                   | デザインサーフェイス上の同じピアレベルの次のオブジェクトにフォーカスと選択を移動します。                                                                                                                                          |
| **Shift + Tab**                                                                           | 前のピア               | デザインサーフェイス上の同じピアレベルで、フォーカスと選択項目を前のオブジェクトに移動します。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 次のピア (フォーカス)           | 次のピアと同じですが、フォーカスを移動して選択するのではなく、フォーカスを移動します。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 前のピア (フォーカス)       | 前のピアと同じですが、フォーカスを移動して選択するのではなく、フォーカスを移動します。                                                                                                                                                       |
| **&lt;**                                                                                | 上昇                      | は、階層内で1レベル上のデザインサーフェイス上の次のオブジェクトに移動します。 階層内のこの図形の上に図形が存在しない場合 (つまり、オブジェクトがデザインサーフェイスに直接配置されている場合)、ダイアグラムが選択されます。 |
| **&gt;**                                                                                | 下がら                     | この階層内の1つ下のレベルにある、デザインサーフェイス上の次に含まれるオブジェクトに移動します。 含まれているオブジェクトが存在しない場合、これは何も行われません。                                                                              |
| **Ctrl + &lt;**                                                                         | Ascend (フォーカス)              | Ascend コマンドと同じですが、選択せずにフォーカスを移動します。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | 降順 (フォーカス)             | [降順] コマンドと同じですが、選択せずにフォーカスを移動します。                                                                                                                                                                         |
| **Shift + End**                                                                         | 接続先に従う         | エンティティから、このエンティティが接続されているエンティティに移動します。                                                                                                                                                               |
| **Delete**                                                                                 | 削除                      | ダイアグラムからオブジェクトまたはコネクタを削除します。                                                                                                                                                                                     |
| **アドイン**                                                                                 | 挿入                      | "スカラープロパティ" コンパートメントヘッダーまたはプロパティ自体が選択されている場合は、新しいプロパティをエンティティに追加します。                                                                                                           |
| **Pg**                                                                               | 図を上にスクロール           | デザインサーフェイスを上にスクロールします。現在表示されているデザインサーフェイスの高さの75% に相当します。                                                                                                                    |
| **Pg**                                                                             | 図を下にスクロール         | デザインサーフェイスを下にスクロールします。                                                                                                                                                                                                    |
| **Shift + Pg**                                                                     | ダイアグラムを右にスクロール        | デザインサーフェイスを右にスクロールします。                                                                                                                                                                                            |
| **Shift + Pg**                                                                       | ダイアグラムを左にスクロール         | デザインサーフェイスを左にスクロールします。                                                                                                                                                                                             |
| **F2**                                                                                  | 編集モードを開始する             | テキストコントロールの編集モードを入力するための標準のキーボードショートカット。                                                                                                                                                               |
| **Shift + F10**                                                                         | ショートカット メニューを表示する       | 選択した項目のショートカットメニューを表示するための標準のキーボードショートカット。                                                                                                                                                          |
| **コントロール + Shift + マウスの左クリック**  <br/> **Control + Shift + マウスホイール forward**  | セマンティックズームイン            | マウスポインターの下にあるダイアグラムビューの領域にズームインします。                                                                                                                                                                 |
| **Ctrl + Shift + マウス右クリック** <br/> **Control + Shift + マウスホイール後方** | セマンティックズームアウト           | ダイアグラムビューの領域をマウスポインターの下にズームアウトします。 現在のダイアグラムの中心を拡大しすぎたときにダイアグラムを中央に配置します。                                                                          |
| **Ctrl + Shift + ' + '** <br/> **Control + マウスホイール forward**                        | 拡大                     | ダイアグラムビューの中央にズームインします。                                                                                                                                                                                         |
| **Control + Shift + '-'** <br/> **Control + マウスホイール後方**                       | 縮小                    | ダイアグラムビューのクリックされた領域からズームアウトします。 現在のダイアグラムの中心を拡大しすぎたときにダイアグラムを中央に配置します。                                                                                            |
| **Ctrl + Shift + マウスの左ボタンを押しながら四角形を描画する**                  | ズーム領域                   | 選択した領域の中央にズームします。 Ctrl キーを押しながら Shift キーを押すと、カーソルが虫眼鏡に変わり、拡大する領域を定義できるようになります。                        |
| **コンテキストメニューキー + m '**                                                              | [マッピングの詳細] ウィンドウを開く | [マッピングの詳細] ウィンドウを開き、選択したエンティティのマッピングを編集します。                                                                                                                                                               |

## <a name="mapping-details-window"></a>[マッピングの詳細] ウィンドウ

![マッピングの詳細のショートカット](~/ef6/media/mappingdetailsshortcuts.png)

| ショートカット                  | アクション         | Notes                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **タブ**                   | コンテキストの切り替え | メインウィンドウ領域と左側のツールバーを切り替える                                                                     |
| **方向キー**            | 「ナビゲーション」     | メインウィンドウ領域の上下左右の列に移動します。 左側のツールバーのボタン間を移動します。 |
| **Enter** <br/> **行間** | Select         | 左側のツールバーでボタンを選択します。                                                                                          |
| **Alt + ↓**      | リストを開く      | ドロップダウンリストを持つセルが選択されている場合は、一覧をドロップダウンします。                                                                     |
| **Enter**                 | リストの選択    | ドロップダウンリスト内の要素を選択します。                                                                                               |
| **Esc**                   | リストを閉じる     | ドロップダウンリストを閉じます。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio のナビゲーション

Entity Framework には、カスタムキーボードショートカットを割り当てることができるいくつかのアクションも用意されています (既定ではショートカットはマップされません)。 これらのカスタムショートカットを作成するには、[ツール] メニューの [オプション] をクリックします。[環境] の [キーボード] を選択します。中央にある一覧を下にスクロールして目的のコマンドを選択し、[ショートカットキー] ボックスにショートカットを入力して、[割り当て] をクリックします。 使用できるショートカットは次のとおりです。

| ショートカット                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus. Microsoft Dataentitydesigncontext. ComplexProperty. Complexproperty**        |
| **OtherContextMenus。 Microsoft DataentitydesignAddCodeGenerationItem**                   |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. AddFunctionImport**                       |
| **OtherContextMenus。 Microsoft DataentitydesignAddEnumType**                      |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. AddNew. Association**                      |
| **OtherContextMenus. Microsoft Dataentitydesignのプロパティ**                  |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. AddNew. ComplexType**                      |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext。**                           |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. の FunctionImport**                   |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. AddNew. 継承**                      |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. AddNew. NavigationProperty**               |
| **OtherContextMenus。 Microsoft DataentitydesignScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. AddtoDiagram**                            |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Close**                                   |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. 折りたたみ**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. ConverttoEnum**                           |
| **OtherContextMenus. CollapseAll (Microsoft Dataentitydesigncontext)**                     |
| **OtherContextMenus. Microsoft Dataentitydesigncontext.................. ExpandAll**                       |
| **OtherContextMenus. ExportasImage (Microsoft Dataentitydesigncontext)**                   |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. 図. LayoutDiagram**                   |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext。編集**                                    |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. EntityKey**                               |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. 展開**                                  |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. FunctionImportMapping**                   |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. GenerateDatabasefromModel**               |
| **OtherContextMenus。 Microsoft DataentitydesignGoToDefinition**                          |
| **OtherContextMenus. ShowGrid (Microsoft Dataentitydesigncontext)**                           |
| **OtherContextMenus. SnaptoGrid (Microsoft Dataentitydesigncontext)**                         |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. インクルード**                          |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. MappingDetails**                          |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. ModelBrowser**                            |
| **OtherContextMenus。 Microsoft DataentitydesignMoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. MovetonewDiagram**                        |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Open**                                    |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. 名前の変更**                                  |
| **OtherContextMenus. ScalarPropertyFormat (Microsoft Dataentitydesigncontext)**        |
| **他のコンテキストメニュー. ScalarPropertyFormat. DisplayNameandType** |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. BaseType**                         |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Select. Entity**                           |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Select. プロパティ**                         |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. サブタイプを選択します。**                          |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. SelectAll**                               |
| **OtherContextMenus。 Microsoft DataentitydesignSelectAssociation**                       |
| **OtherContextMenus。 Microsoft DataentitydesignShowinDiagram**                           |
| **OtherContextMenus。 Microsoft DataentitydesignShowinModelBrowser**                      |
| **OtherContextMenus。 Microsoft DataentitydesignStoredProcedureMapping**                  |
| **OtherContextMenus。 Microsoft DataentitydesignTableMapping**                            |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext。 UpdateModelfromDatabase**                 |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext。 Validate**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 10**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 100**                                |
| **OtherContextMenus。 Microsoft Dataentitydesigncontext. Zoom. 125**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 150**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 200**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 25**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 300**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 33**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 400**                                |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 50**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 66**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. 75**                                 |
| **OtherContextMenus. Microsoft Dataentitydesigncontext. Zoom. Custom**                             |
| **OtherContextMenus. Microsoft DataentitydesignZoomIn**                             |
| **OtherContextMenus. Microsoft DataentitydesignZoomOut**                            |
| **OtherContextMenus. Microsoft DataentitydesignZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
