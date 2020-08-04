---
title: EF Core 1.0 RC2 から RTM-EF Core へのアップグレード
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526772"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>EF Core 1.0 RC2 から RTM へのアップグレード

この記事では、RC2 パッケージでビルドされたアプリケーションを 1.0.0 RTM に移動するためのガイダンスを提供します。

## <a name="package-versions"></a>パッケージのバージョン

アプリケーションに通常インストールする最上位レベルのパッケージの名前は、RC2 と RTM の間で変更されませんでした。

**インストールされているパッケージを RTM バージョンにアップグレードする必要があります。**

* ランタイムパッケージ (たとえば、 `Microsoft.EntityFrameworkCore.SqlServer` ) はからに変更されました `1.0.0-rc2-final` `1.0.0` 。

* `Microsoft.EntityFrameworkCore.Tools`パッケージがからに変更されました `1.0.0-preview1-final` `1.0.0-preview2-final` 。 ツールはまだプレリリースであることに注意してください。

## <a name="existing-migrations-may-need-maxlength-added"></a>既存の移行に maxLength を追加する必要がある

RC2 では、移行の列の定義は、 `table.Column<string>(nullable: true)` 移行の背後にあるコードに格納しているいくつかのメタデータで検索されています。 RTM では、スキャフォールディングコードに長さが含まれるようになりました `table.Column<string>(maxLength: 450, nullable: true)` 。

RTM を使用する前にスキャフォールディングしていた既存の移行には、引数が指定されていません `maxLength` 。 これは、データベースでサポートされている最大長 (SQL Server) が使用されることを意味し `nvarchar(max)` ます。 これは一部の列に対しては問題ありませんが、キー、外部キー、またはインデックスの一部である列は、最大長を含むように更新する必要があります。 慣例により、450はキー、外部キー、およびインデックス付き列に使用される最大長です。 モデルで長さが明示的に構成されている場合は、代わりにその長さを使用する必要があります。

### <a name="aspnet-identity"></a>ASP.NET Identity

この変更は、ASP.NET Identity を使用し、RTM 以前のプロジェクトテンプレートから作成されたプロジェクトに影響します。 プロジェクトテンプレートには、データベースの作成に使用される移行が含まれています。 この移行を編集して、次の列の最大長を指定する必要があり `256` ます。

* **AspNetRoles**
  * 名前
  * NormalizedName
* **AspNetUsers**
  * 電子メール
  * NormalizedEmail
  * NormalizedUserName
  * UserName

この変更を行わないと、最初の移行がデータベースに適用されるときに、次の例外が発生します。

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core: project.jsの [インポート] を削除します。

.NET Core を RC2 で対象としていた場合は、 `imports` .NET Standard をサポートしていない EF Core の依存関係の一部に対する一時的な回避策として project.jsにを追加する必要がありました。 これらは削除できます。

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
> バージョン 1.0 RTM の時点では、 [.NET Core SDK](https://www.microsoft.com/net/download/core)は `project.json` Visual Studio 2015 を使用して .net Core アプリケーションをサポートまたは開発しなくなりました。 [project.json から csproj への移行](/dotnet/articles/core/migration/)をお勧めします。 Visual Studio を使用している場合は、 [Visual studio 2017](https://www.visualstudio.com/downloads/)にアップグレードすることをお勧めします。

## <a name="uwp-add-binding-redirects"></a>UWP: バインドリダイレクトの追加

ユニバーサル Windows プラットフォーム (UWP) プロジェクトで EF コマンドを実行しようとすると、次のエラーが発生します。

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

UWP プロジェクトにバインドリダイレクトを手動で追加する必要があります。 プロジェクトルートフォルダーにという名前のファイルを作成 `App.config` し、適切なアセンブリバージョンにリダイレクトを追加します。

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
