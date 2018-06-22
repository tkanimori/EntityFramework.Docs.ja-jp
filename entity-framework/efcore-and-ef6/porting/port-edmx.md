---
title: EF6 から EF Core - 移植 EDMX ベースのモデルへの移植
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: c999d2114c76ee3a7615ae897b42ee3376cff14e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812691"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>EF core EF6 EDMX に基づくモデルの移植

EF Core では、モデルの EDMX ファイルの形式はサポートしません。 これらのモデルに移植する最適なオプションでは、アプリケーションのデータベースから新しいコードに基づくモデルを生成します。

## <a name="install-ef-core-nuget-packages"></a>EF Core NuGet パッケージをインストールします。

インストール、 `Microsoft.EntityFrameworkCore.Tools` NuGet パッケージです。

## <a name="regenerate-the-model"></a>モデルを再生成します。

既存のデータベースに基づくモデルを作成する、リバース エンジニア リングの機能を使えるようになりました。

パッケージ マネージャー コンソールで、次のコマンドを実行 (ツールは NuGet Package Manager をポイント パッケージ マネージャー コンソールのポイント)。 参照してください[Package Manager Console (Visual Studio)](../../core/miscellaneous/cli/powershell.md)テーブルなどのサブセットをスキャフォールディングするコマンド オプションについてはします。

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

たとえば、SQL Server LocalDB インスタンス上のブログ データベースからモデルをスキャフォールディングするコマンドを次に示します。

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>EF6 モデルを削除します。

今すぐ、アプリケーションから EF6 モデルを削除します。

これは正常に EF6 NuGet パッケージ (EntityFramework) をインストールすると、そのまま EF コアおよび EF6 が同じアプリケーションに並列で使用できます。 ただし、アプリケーションのすべての領域で EF6 を使用する場合は、パッケージをアンインストールに役立つの注意が必要なコードのコンパイル エラーが発生します。

## <a name="update-your-code"></a>コードを更新します

この時点では、アドレス指定のコンパイル エラーと EF6 と EF コア間の動作の変更に影響するかどうかを確認するコードの問題であります。

## <a name="test-the-port"></a>ポートをテストします。

アプリケーションをコンパイルするためにだけ限りません EF コアが正常に移植します。 動作の変更のいずれもと、アプリケーションが悪影響を受けることを確認するには、アプリケーションのすべての領域をテストする必要があります。

> [!TIP]
> 参照してください[EF Core 既存のデータベースでの ASP.NET Core の使用を開始する](xref:core/get-started/aspnetcore/existing-db)既存のデータベースを操作する方法に関するその他のリファレンスについては 
