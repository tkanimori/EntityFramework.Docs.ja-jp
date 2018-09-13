---
title: EF デザイナー モデル - EF6 の Entity Framework ランタイムのバージョンを選択します。
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488492"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>Entity Framework ランタイムのバージョンの EF デザイナー モデルの選択
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 以降、次の画面は、モデルの作成時に対象にするランタイムのバージョンを選択するように EF Designer に追加されました。 Entity Framework の最新バージョンが既にプロジェクトにインストールされていないときに、画面が表示されます。 最新のバージョンが既にインストールされている場合のみ既定で使用できます。

![画面](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a>EF6.x を対象とします。

EF6 は EF6 ランタイム プロジェクトを追加する [バージョンの選択] 画面から選択できます。 EF6 を追加すると、現在のプロジェクトでは、この画面が表示されるを停止します。

以前のバージョンの EF が (同じプロジェクトからランタイムの複数のバージョンを対象とすることはできません) からのインストールが既にある場合は、EF6 を無効化されます。 EF6 のオプションが有効にしない場合は、プロジェクトを EF6 にアップグレードする手順に従います。

1.  ソリューション エクスプ ローラーでプロジェクトを右クリックして**NuGet パッケージの管理.**
2.  選択**更新プログラム**
3.  選択**EntityFramework** (必要なバージョンに更新することを確認してください)
4.  クリックして**Update**

 

## <a name="targeting-ef5x"></a>EF5.x を対象とします。

EF5 EF5 ランタイム プロジェクトを追加する [バージョンの選択] 画面から選択できます。 EF5 を追加したら、EF6 のオプションを無効になっていますが、画面が表示されます。

既にインストールされているランタイムの EF4.x バージョンがあると、そのバージョンの EF EF5 ではなく、画面に表示が表示されます。 EF5 にアップグレードするこのような状況で、次の手順を使用します。

1.  選択**ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**
2.  実行 **"Install-package entityframework"-バージョン 5.0.0**

 

## <a name="targeting-ef4x"></a>EF4.x を対象とします。

EF4.x ランタイムは、次の手順を使用して、プロジェクトにインストールできます。

1.  選択**ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**
2.  実行 **"Install-package entityframework"、バージョン 4.3.0**
