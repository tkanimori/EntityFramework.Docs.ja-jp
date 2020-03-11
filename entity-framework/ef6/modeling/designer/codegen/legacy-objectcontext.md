---
title: Entity Framework Designer の ObjectContext に戻す-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
ms.openlocfilehash: 3e436f0d9cf94720be9c424b327816438d571ae8
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415431"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Entity Framework Designer 内の ObjectContext に戻す
以前のバージョンの Entity Framework は、EF デザイナーで作成されたモデルが、EntityObject から派生した ObjectContext およびエンティティクラスから派生したコンテキストを生成します。

EF 4.1 以降では、DbContext および POCO エンティティクラスから派生するコンテキストを生成するコード生成テンプレートにスワップすることをお勧めします。

Visual Studio 2012 では、EF デザイナーで作成されたすべての新しいモデルに対して、既定で DbContext コードが生成されます。 DbContext ベースのコードジェネレーターにスワップする場合を除き、既存のモデルでは、ObjectContext ベースのコードが引き続き生成されます。

## <a name="reverting-back-to-objectcontext-code-generation"></a>ObjectContext コード生成に戻す

### <a name="1-disable-dbcontext-code-generation"></a>1. DbContext コード生成を無効にします

派生した DbContext と POCO クラスの生成は、プロジェクト内の2つの .tt ファイルによって処理されます。ソリューションエクスプローラーで .edmx ファイルを展開すると、これらのファイルが表示されます。 これらの両方のファイルをプロジェクトから削除します。

![コード Gen ファイル](~/ef6/media/codegenfiles.png)

VB.NET を使用している場合は、 **[すべてのファイルを表示]** ボタンを選択して、入れ子になったファイルを表示する必要があります。

![すべてのファイルを表示](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. ObjectContext コード生成を再度有効にする

EF デザイナーでモデルを開き、デザイン画面の空白部分を右クリックして、 **[プロパティ]** を選択します。

プロパティウィンドウ **[コード生成方法]** を **[なし**] から **[既定]** に変更します。

![コード生成戦略](~/ef6/media/codegenstrategy.png)
