---
title: RTM - に 1.0 RC2 をコア EF からアップグレードする EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 1b95b2ab1943dfb541b3a7c873cff3cb4c16d9c1
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998320"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>EF Core 1.0 RC2 から RTM にアップグレードします。

この記事では、1.0.0 に RC2 パッケージで構築されたアプリケーションの移動のガイダンスを提供します。 RTM。

## <a name="package-versions"></a>パッケージのバージョン

アプリケーションに通常インストールされる最上位レベルのパッケージの名前 RC2 から RTM の間で変更していません。

**RTM バージョンにインストールされているパッケージをアップグレードする必要があります。**

* ランタイム パッケージ (たとえば、 `Microsoft.EntityFrameworkCore.SqlServer`) から変更`1.0.0-rc2-final`に`1.0.0`します。

* `Microsoft.EntityFrameworkCore.Tools`からパッケージが変更された`1.0.0-preview1-final`に`1.0.0-preview2-final`します。 ツールはまだプレリリース版であることに注意してください。

## <a name="existing-migrations-may-need-maxlength-added"></a>既存の移行が maxLength の追加を必要があります。

移行では、列定義のように RC2 では、`table.Column<string>(nullable: true)`され、列の長さが、移行の分離コードで格納するいくつかのメタデータで検索します。 長さに今すぐスキャフォールディングされたコードに含まれている RTM では、`table.Column<string>(maxLength: 450, nullable: true)`します。

RTM を使用する前にスキャフォールディングされた、既存の移行はありません、`maxLength`引数が指定されました。 つまり、データベースでサポートされている最大の長さが使用されます (`nvarchar(max)` SQL サーバー上)。 これは問題ありませんが、一部の列、列、キー、外部のキーの一部である可能性があります。 またはインデックスは、最大長を含むように更新する必要があります。 通常、450 は最大長は、キー、外部キーは、使用され、インデックス列を作成します。 モデルで明示的に長さを構成した場合がその長さ代わりに使用します。

**ASP.NET Identity**

この変更の影響を ASP.NET Identity を使用し、前から作成されたプロジェクトのプロジェクト テンプレートを RTM。 プロジェクト テンプレートには、データベースの作成に使用される移行が含まれています。 この移行を編集の最大長を指定する必要があります`256`次の列にします。

*  **AspNetRoles**

    * name

    * NormalizedName

*  **AspNetUsers**

   * 電子メール

   * NormalizedEmail

   * NormalizedUserName

   * UserName

最初の移行がデータベースに適用されるときに、この変更を行う失敗は次の例外が発生されます。

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a>.NET core: project.json で"imports"を削除します。

追加する必要 RC2 を使用して .NET Core を対象とした場合`imports`を一部の .NET Standard をサポートしない EF Core の依存関係の一時的な回避策として、project.json にします。 これらは削除ようになりましたことができます。

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
> バージョン 1.0 の時点で RTM、 [.NET Core SDK](https://www.microsoft.com/net/download/core)はサポートしなくなりました`project.json`または Visual Studio 2015 を使用して .NET Core アプリケーションを開発します。 [project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。 Visual Studio を使用している場合にアップグレードする勧め[Visual Studio 2017](https://www.visualstudio.com/downloads/)します。

## <a name="uwp-add-binding-redirects"></a>UWP: バインド リダイレクトを追加します。

ユニバーサル Windows プラットフォーム (UWP) プロジェクトの結果、次のエラーで EF コマンドを実行しようとしています。

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

UWP プロジェクトにバインド リダイレクトを手動で追加する必要があります。 という名前のファイルを作成する`App.config`プロジェクトのルート フォルダーと、適切なアセンブリのバージョンにリダイレクトを追加します。

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
