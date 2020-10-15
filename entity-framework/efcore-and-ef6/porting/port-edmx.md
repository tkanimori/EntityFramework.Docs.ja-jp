---
title: EF6 から EF Core への移植 - EDMX ベース モデルの移植 - EF
description: Entity Framework 6 つの EDMX ベース モデル アプリケーションを Entity Framework Core に移植する方法に関する固有の情報
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 27b37ad1c2e3436ae96a71bc97e953763c48ee50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064264"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>EF Core への EF6 EDMX ベース モデルの移植

EF Core では EDMX ファイル形式のモデルをサポートしていません。 これらのモデルを移植する最良の方法は、アプリケーションのデータベースから新しいコードベースのモデルを生成することです。

## <a name="install-ef-core-nuget-packages"></a>EF Core の NuGet パッケージをインストールする

`Microsoft.EntityFrameworkCore.Tools` NuGet パッケージをインストールします。

## <a name="regenerate-the-model"></a>モデルを再生成する

リバース エンジニアリング機能を使用して、既存のデータベースに基づくモデルを作成できるようになりました。

パッケージ マネージャー コンソールで [ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール] のコマンドを実行します。 テーブルのサブセットをスキャフォールディングするコマンド オプションなどについては、[Visual Studio のパッケージ マネージャー コンソール](xref:core/miscellaneous/cli/powershell)を参照してください。

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

たとえば、SQL Server LocalDB インスタンスのブログ データベースからモデルをスキャフォールディングするコマンドを次に示します。

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>EF6 モデルを削除する

ここでは、アプリケーションから EF6 モデルを削除します。

EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションで並列で使用できるようにするためです。 ただし、アプリケーションのどの領域でも EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコード上にコンパイル エラーを表示することができます。

## <a name="update-your-code"></a>コードを更新する

この時点では、コンパイル エラーに対処し、コードを見直して、EF6 から EF Core への動作変更の影響があるかどうかを確認します。

## <a name="test-the-port"></a>移植をテストする

アプリケーションでコンパイルされるからといって、EF Core に正常に移植されるわけではありません。 アプリケーションのすべての領域をテストして、どの動作変更もアプリケーションに悪影響を与えないことを確認する必要があります。
