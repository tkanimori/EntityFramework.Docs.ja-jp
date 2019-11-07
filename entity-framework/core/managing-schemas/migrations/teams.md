---
title: チーム環境での移行-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 6c17c56277821159962884aef72d46c624442e20
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655541"
---
# <a name="migrations-in-team-environments"></a>チーム環境での移行

チーム環境で移行を行う場合は、モデルスナップショットファイルに特に注意を払ってください。 このファイルを使用すると、チームメイトの移行が正常にマージされたかどうかや、共有前に移行を再作成して競合を解決する必要があるかどうかがわかります。

## <a name="merging"></a>マージ

チームメイトからの移行をマージすると、モデルスナップショットファイルで競合が発生する可能性があります。 両方の変更が関連付けられていない場合、マージは簡単で、2つの移行を共存させることができます。 たとえば、次のような customer エンティティ型構成では、マージの競合が発生する可能性があります。

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

これらの両方のプロパティが最終的なモデルに存在する必要があるため、両方のプロパティを追加してマージを完了します。 多くの場合、このような変更は、バージョン管理システムによって自動的にマージされることがあります。

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

このような場合、移行とチームメイトの移行は互いに独立しています。 これらのいずれかを最初に適用することができるため、チームと共有する前に、移行に対して追加の変更を行う必要はありません。

## <a name="resolving-conflicts"></a>競合の解決

モデルスナップショットモデルをマージするときに、実際の競合が発生することがあります。 たとえば、自分とチームメイトが、それぞれ同じプロパティの名前を変更したとします。

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

この種類の競合が発生した場合は、移行を再作成して解決します。 この場合は、以下の手順に従ってください。

1. マージの前に、マージを中止して作業ディレクトリにロールバックします
2. 移行を削除する (ただし、モデルの変更を保持する)
3. チームメイトの変更を作業ディレクトリにマージする
4. 移行を再追加する

その後、2つの移行を正しい順序で適用できます。 最初に移行が適用され、列の名前が*別名*に変更されます。その後、移行によって*ユーザー名*に名前が変更されます。

移行は、チームの他のメンバーと安全に共有できます。
