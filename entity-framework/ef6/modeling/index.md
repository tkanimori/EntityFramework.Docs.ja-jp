---
title: モデルの作成 - EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 4890228E-CEA1-4595-B8AD-CA81253F8767
ms.openlocfilehash: b0b9125acb7dccbedf06b64513c0b2b2c4d8e1b1
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489715"
---
# <a name="creating-a-model"></a>モデルの作成

EF モデルは、アプリケーションのクラスとプロパティがデータベース テーブルと列にどのようにマップされるかに関する詳細を格納します。 EF モデルは、主に次の 2 とおりの方法で作成できます。

- **Code First を使用する**: 開発者がコードを記述してモデルを指定します。 EF では、エンティティ クラスと開発者によって提供された追加のモデル構成に基づき、ランタイムにモデルとマッピングを生成します。

- **EF Designer を使用する**: 開発者が EF Designer を使用してボックスと線を描画し、モデルを指定します。 結果として得られるモデルは、EDMX 拡張子を持つファイルに XML として格納されます。 アプリケーションのドメイン オブジェクトは通常、概念モデルから自動的に生成されます。

## <a name="ef-workflows"></a>EF のワークフロー

どちらの方法も、既存のデータベースを対象にしたり、新しいデータベースを作成したりするために使用できます。その結果、4 種類のワークフローが生成されます。
最適な方法を確認してください。  

|                                           | コードを記述したい...                                                                                                                   | デザイナーを使いたい...                                                                                                                        |
|:------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
| **新しいデータベースを作成している**          | [**Code First** を使ってコードでモデルを定義してから、データベースを生成します。](~/ef6/modeling/code-first/workflows/new-database.md)           | [**Model First** を使ってボックスと線でモデルを定義してから、データベースを生成します。](~/ef6/modeling/designer/workflows/model-first.md)   |
| **既存のデータベースにアクセスする必要がある** | [**Code First** を使って、既存のデータベースにマップするコードベースのモデルを作成します。](~/ef6/modeling/code-first/workflows/existing-database.md) | [**Database First** を使って、既存のデータベースにマップする、ボックスと線のモデルを作成します。](~/ef6/modeling/designer/workflows/database-first.md) |

### <a name="watch-the-video-what-ef-workflow-should-i-use"></a>ビデオを見る: どのような EF のワークフローを使用する必要がありますか。

この短いビデオでその違いと、最適なものを見つける方法を説明します。

**提供**: [Rowan Miller](http://romiller.com/)

![Which Workflow Thumb](../media/whichworkflow-thumb.png) [WMV](http://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.wmv) | [MP4](http://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_mp4video_ChoseYourWorkflow.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.zip)

ビデオを見た後も EF Designer と Code First のどちらを使うべきか決められない場合は、どちらも学んでみましょう。

## <a name="a-look-under-the-hood"></a>内部的な処理

Code First と EF Designer のどちらを使っても、EF モデルには常に次のような複数のコンポーネントが含まれています。

- アプリケーションのドメイン オブジェクト、またはエンティティ型自体。 これはしばしば、オブジェクト レイヤーと呼ばれます

- [Entity Data Model](~/ef6/resources/glossary.md#entity-data-model) で説明される、ドメイン固有のエンティティ型とリレーションシップで構成される概念モデル。 このレイヤーはしばしば、文字 "C" (_概念_) で表されます。

- データベースで定義されているテーブル、列およびリレーションシップを表すストレージ モデル。 このレイヤーはしばしば、文字 "S" (_ストレージ_) で表されます。  

- 概念モデルとデータベース スキーマ間のマッピング。 このマッピングはしばしば、"C-S" マッピングと呼ばれます。

EF のマッピング エンジンは、"C-S" マッピングを利用してエンティティに対する作成、読み取り、更新、削除などの操作を、データベースのテーブルに対する同等の操作に変換します。

概念モデルとアプリケーションのオブジェクト間のマッピングは、しばしば "O-C" マッピングと呼ばれます。 "C-S" マップと比較すると、"O-C" のマッピングは暗黙的で、一対一です.NET オブジェクトの図形と種類が一致するには、概念モデルに定義されたエンティティ、プロパティ、リレーションシップが必要です。 EF4 以降は、EF 上での依存関係のないプロパティによる単純なオブジェクトで、オブジェクト レイヤーを構成することができます。 これらは通常、Plain-Old CLR Objects (POCO) と呼ばれ、型とプロパティのマッピングは名前の照合規則に基づいて実行されます。 以前、EF 3.5 では、"O-C" マッピングを実装するには、エンティティが EntityObject クラスから派生しないといけなかったり、EF 属性を運ばなければならないような、オブジェクト レイヤーの特定の制約がありました。
