---
title: EF6 から EF Core への移植 - EDMX ベースのモデルの移植
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 2c3336ac675a830566001a0ddb3777839f52db18
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997412"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>EF Core に EF6 EDMX ベース モデルの移植

EF Core は、モデルの EDMX ファイルの形式をサポートしていません。 これらのモデルに移植する最適なオプションでは、アプリケーションのデータベースから新しいコード ベースのモデルを生成します。

## <a name="install-ef-core-nuget-packages"></a>EF Core NuGet パッケージをインストールします。

インストール、 `Microsoft.EntityFrameworkCore.Tools` NuGet パッケージ。

## <a name="regenerate-the-model"></a>モデルを再生成します。

既存のデータベースに基づくモデルを作成する、リバース エンジニア リング機能を使えるようになりました。

パッケージ マネージャー コンソールで、次のコマンドを実行 (ツールが NuGet パッケージ マネージャーを -> パッケージ マネージャー コンソールを ->)。 参照してください[パッケージ マネージャー コンソール (Visual Studio)](../../core/miscellaneous/cli/powershell.md)コマンド オプションのテーブルなどのサブセットをスキャフォールディングします。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

たとえば、SQL Server LocalDB インスタンスで Blogging データベースからモデルをスキャフォールディングするコマンドを示します。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>EF6 モデルを削除します。

EF6 モデルをアプリケーションから削除するとようになりました。

これを EF6 の NuGet パッケージ (EntityFramework) をインストールすると、そのままに EF Core と EF6 が同じアプリケーションで並列で使用できます。 ただし、アプリケーションの領域で EF6 を使用する場合は、パッケージをアンインストールし、役立つの注意が必要なコードのコンパイル エラーが発生します。

## <a name="update-your-code"></a>コードを更新します

この時点では、コンパイル エラーをアドレス指定と EF6 と EF Core の動作の変更に影響するかどうかを確認コードの問題になります。

## <a name="test-the-port"></a>ポートをテストします。

アプリケーションをコンパイルするためにだけしないわけでは EF Core に移植が正常にします。 None、動作の変更と、アプリケーションが悪影響を受けることを確認するアプリケーションのすべての領域をテストする必要があります。

> [!TIP]
> 参照してください[既存のデータベースでの ASP.NET Core での EF Core の概要](xref:core/get-started/aspnetcore/existing-db)既存のデータベースを操作する方法に関するその他のリファレンスについては 
