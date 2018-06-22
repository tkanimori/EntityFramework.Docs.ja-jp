---
title: RTM - する 1.0 RC2 EF からアップグレードするコア EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 4bb4c5736708413f6581cad250b089b7bc22a559
ms.sourcegitcommit: 90139dbd6f485473afda0788a5a314c9aa601ea0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30151041"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>EF Core 1.0 RC2 から RTM にアップグレードします。

この記事 1.0.0 の RC2 パッケージにビルドされたアプリケーションの移動のガイダンスを提供する RTM です。

## <a name="package-versions"></a>パッケージのバージョン

アプリケーションをインストールするには通常最上位のパッケージの名前は、RC2 と RTM の間で変わらないです。

**RTM バージョンにインストールされているパッケージをアップグレードする必要があります。**

* 実行時のパッケージ (例: `Microsoft.EntityFrameworkCore.SqlServer`) から変更`1.0.0-rc2-final`に`1.0.0`です。

* `Microsoft.EntityFrameworkCore.Tools`からパッケージが変更された`1.0.0-preview1-final`に`1.0.0-preview2-final`です。 ツールがまだプレリリース版であることを注意してください。

## <a name="existing-migrations-may-need-maxlength-added"></a>既存の移行は、追加 maxLength を必要があります。

RC2 では、移行では、列定義と同様に参照`table.Column<string>(nullable: true)`列の長さが、移行の分離コードの格納のメタデータの一部で検索したとします。 RTM では、長さが今すぐスキャフォールディング コードにインクルード`table.Column<string>(maxLength: 450, nullable: true)`です。

RTM を使用する前にスキャフォールディングされましたが、既存の移行はありません、`maxLength`引数が指定されています。 つまり、データベースでサポートされている最大の長さが使用されます (`nvarchar(max)` SQL Server 上)。 一部の列がキー、外部キーの一部である列の可能性があります。 またはインデックスは、最大長を含まれるように更新する必要があります。 規則では、450 は、最大長キーについては、外部キーは、使用されて、列のインデックスを作成します。 モデルで、長さが明示的に構成されている場合する必要がありますを使用してその長さ。

**ASP.NET Identity**

この変更に影響を ASP.NET の Id を使用し、以前から作成されたプロジェクトのプロジェクト テンプレートを RTM です。 プロジェクト テンプレートには、データベースの作成に使用される移行が含まれています。 最大長を指定するこの移行を編集する必要があります`256`次の列にします。

*  **AspNetRoles**

    * 名前

    * NormalizedName

*  **AspNetUsers**

   * 電子メール

   * NormalizedEmail

   * NormalizedUserName

   * UserName

最初の移行をデータベースに適用すると、この変更を行うには、次の例外になります。

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a>.NET core: project.json に「インポート」を削除します。

追加する必要がある RC2 を使用して .NET Core をターゲットとするが場合、 `imports` EF コアの依存関係をサポートしていない .NET 標準の一部の一時的な回避策として project.json にします。 ここで削除できます。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> バージョン 1.0 の時点で RTM、 [.NET Core SDK](https://www.microsoft.com/net/download/core)はサポートしなくなりました`project.json`または Visual Studio 2015 を使用して .NET Core アプリケーションを開発します。 [project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。 Visual Studio を使用している場合にアップグレードする勧め[Visual Studio 2017](https://www.visualstudio.com/downloads/)です。

## <a name="uwp-add-binding-redirects"></a>UWP: は、バインド リダイレクトを追加します。

ユニバーサル Windows プラットフォーム (UWP) プロジェクトの結果、次のエラーで EF コマンドを実行しようとしています。

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

UWP プロジェクトにバインド リダイレクトを手動で追加する必要があります。 という名前のファイルを作成する`App.config`プロジェクトのルート フォルダーと、正しいバージョンのアセンブリにリダイレクトを追加します。

``` xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
