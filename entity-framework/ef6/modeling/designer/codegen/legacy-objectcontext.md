---
title: Entity Framework デザイナー - EF6 で ObjectContext に戻しています
author: divega
ms.date: 2016-10-23
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
ms.openlocfilehash: b52bfc36c97e1a3c7cd2d3716feb1ae48c68a56e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997313"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Entity Framework デザイナーで ObjectContext に戻しています
以前のバージョンの Entity Framework EF Designer で作成したモデルの ObjectContext から派生したコンテキストと EntityObject から派生したエンティティ クラスは生成します。

EF4.1 以降 DbContext と POCO エンティティ クラスから派生したコンテキストを生成するコード生成テンプレートにスワップお勧めします。

Visual Studio 2012 では、既定では EF Designer で作成されたすべての新しいモデルの生成された DbContext コードを取得します。 既存のモデルでは、引き続き DbContext ベースのコード ジェネレーターをスワップしない限り、ベース ObjectContext コードを生成します。

## <a name="reverting-back-to-objectcontext-code-generation"></a>ObjectContext コードの生成に戻します

### <a name="1-disable-dbcontext-code-generation"></a>1.DbContext のコード生成を無効にします。

プロジェクトで 2 つの .tt ファイルによって派生 DbContext と POCO クラスの生成が処理される、ソリューション エクスプ ローラーで .edmx ファイルを展開する場合は、これらのファイルが表示されます。 これらのファイルをプロジェクトから削除します。

![CodeGenFiles](~/ef6/media/codegenfiles.png)

VB.NET を使用している場合は、選択する必要があります。、 **すべてのファイル**入れ子になったファイルを表示するボタンをクリックします。

![ShowAllFiles](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2.ObjectContext コードの生成を再度有効にします。

デザイン サーフェスと選択の空白部分を右クリックし、EF デザイナーでモデル化するオープン**プロパティ**します。

プロパティ ウィンドウの変更で、**コード生成方法**から**None**に**既定**します。

![CodeGenStrategy](~/ef6/media/codegenstrategy.png)
