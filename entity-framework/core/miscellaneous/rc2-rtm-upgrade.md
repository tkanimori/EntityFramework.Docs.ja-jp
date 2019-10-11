---
title: EF Core 1.0 RC2 から RTM-EF Core へのアップグレード
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: e7f121d18931e26e7b5d11842da6da4a9b789efe
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181360"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>EF Core 1.0 RC2 から RTM へのアップグレード

この記事では、RC2 パッケージでビルドされたアプリケーションを 1.0.0 RTM に移動するためのガイダンスを提供します。

## <a name="package-versions"></a>パッケージのバージョン

アプリケーションに通常インストールする最上位レベルのパッケージの名前は、RC2 と RTM の間で変更されませんでした。

**インストールされているパッケージを RTM バージョンにアップグレードする必要があります。**

* ランタイムパッケージ (たとえば、`Microsoft.EntityFrameworkCore.SqlServer`) が `1.0.0-rc2-final` から `1.0.0` に変更されました。

* @No__t 0 のパッケージが `1.0.0-preview1-final` から `1.0.0-preview2-final` に変更されました。 ツールはまだプレリリースであることに注意してください。

## <a name="existing-migrations-may-need-maxlength-added"></a>既存の移行に maxLength を追加する必要がある

RC2 では、移行の列の定義は `table.Column<string>(nullable: true)` のように見え、列の長さは、移行の背後にあるコードに格納しているいくつかのメタデータで検索されました。 RTM では、この長さはスキャフォールディングコード `table.Column<string>(maxLength: 450, nullable: true)` に含まれるようになりました。

RTM を使用する前にスキャフォールディングされていた既存の移行には、@no__t 0 引数が指定されていません。 これは、データベースでサポートされている最大長が使用されることを意味します (SQL Server では `nvarchar(max)`)。 これは一部の列に対しては問題ありませんが、キー、外部キー、またはインデックスの一部である列は、最大長を含むように更新する必要があります。 慣例により、450はキー、外部キー、およびインデックス付き列に使用される最大長です。 モデルで長さが明示的に構成されている場合は、代わりにその長さを使用する必要があります。

**ASP.NET Identity**

この変更は、ASP.NET Identity を使用し、RTM 以前のプロジェクトテンプレートから作成されたプロジェクトに影響します。 プロジェクトテンプレートには、データベースの作成に使用される移行が含まれています。 この移行を編集して、次の列に `256` の最大長を指定する必要があります。

*  **AspNetRoles**

    * 名前

    * NormalizedName

*  **AspNetUsers**

   * Email

   * NormalizedEmail

   * NormalizedUserName

   * UserName

この変更を行わないと、最初の移行がデータベースに適用されるときに、次の例外が発生します。

```console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core:Project. json の "インポート" を削除する

.NET Core を RC2 でターゲットとしている場合は、.NET Standard をサポートしていない EF Core の依存関係のいくつかについて、一時的な回避策として `imports` をプロジェクトに追加する必要がありました。 これらは削除できます。

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
> バージョン 1.0 RTM の時点では、 [.NET Core SDK](https://www.microsoft.com/net/download/core)は `project.json` をサポートしなくなりました。また、Visual Studio 2015 を使用して .net Core アプリケーションを開発することもできなくなりました。 [project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。 Visual Studio を使用している場合は、 [Visual studio 2017](https://www.visualstudio.com/downloads/)にアップグレードすることをお勧めします。

## <a name="uwp-add-binding-redirects"></a>UWPバインドリダイレクトの追加

ユニバーサル Windows プラットフォーム (UWP) プロジェクトで EF コマンドを実行しようとすると、次のエラーが発生します。

```console
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

UWP プロジェクトにバインドリダイレクトを手動で追加する必要があります。 プロジェクトルートフォルダーに `App.config` という名前のファイルを作成し、適切なアセンブリバージョンにリダイレクトを追加します。

```xml
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
