---
title: EF デザイナーモデルに Entity Framework ランタイムバージョンを選択する-EF6
description: Entity Framework 6 の EF デザイナーモデルで Entity Framework ランタイムバージョンを選択する
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 77830aaa3c7365e05e867d4ced88bdb8a11e7d6b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620391"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>EF デザイナーモデルの Entity Framework ランタイムバージョンの選択
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

EF6 以降では、次の画面が EF デザイナーに追加され、モデルの作成時に対象とするランタイムのバージョンを選択できるようになりました。 この画面は、最新バージョンの Entity Framework がプロジェクトにまだインストールされていない場合に表示されます。 最新バージョンが既にインストールされている場合は、既定でのみ使用されます。

![ランタイムバージョンの選択](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>EF6 をターゲットにする

[バージョンの選択] 画面で [EF6] を選択して、EF6 ランタイムをプロジェクトに追加できます。 EF6 を追加すると、現在のプロジェクトでこの画面が表示されなくなります。

以前のバージョンの EF が既にインストールされている場合、EF6 は無効になります (同じプロジェクトから複数のバージョンのランタイムを対象にすることはできません)。 ここで EF6 オプションが有効になっていない場合は、次の手順に従ってプロジェクトを EF6 にアップグレードします。

1.  ソリューションエクスプローラーでプロジェクトを右クリックし、[ **NuGet パッケージの管理**] を選択します。
2.  **更新プログラム**の選択
3.  **Entityframework**を選択します (必要なバージョンに更新することを確認してください)
4.  [**更新**] をクリック

 

## <a name="targeting-ef5x"></a>EF5 をターゲットにする

[バージョンの選択] 画面で [EF5] を選択して、EF5 ランタイムをプロジェクトに追加できます。 EF5 を追加した後も、EF6 オプションが無効になっている画面が表示されます。

EF4 バージョンのランタイムが既にインストールされている場合は、EF5 ではなく、そのバージョンの EF が画面に表示されます。 このような状況では、次の手順を使用して EF5 にアップグレードできます。

1.  **ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソールの**選択
2.  **インストールパッケージ EntityFramework の実行-バージョン 5.0.0**

 

## <a name="targeting-ef4x"></a>EF4 をターゲットにする

EF4 ランタイムをプロジェクトにインストールするには、次の手順を実行します。

1.  **ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソールの**選択
2.  **Install-Package EntityFramework-version 4.3.0 を**実行します。
