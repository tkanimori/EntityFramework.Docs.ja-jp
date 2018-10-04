---
title: Entiy Framework Core のインストール
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 455eccbb149f0980cefa250ef5db443c73e66603
ms.sourcegitcommit: ae399f9f3d1bae2c446b552247bd3af3ca5a2cf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48575640"
---
# <a name="installing-entity-framework-core"></a>Entiy Framework Core のインストール

## <a name="prerequisites"></a>必須コンポーネント

* .NET Core 2.1 を対象とするアプリを開発するには、[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core) をインストールします。 SDK は、Visual Studio 2017 の最新バージョンがある場合でもインストールする必要があります。

* .NET Core 2.1 を対象とするアプリの開発用 Visual Studio を使用するには、Visual Studio 2017 バージョン 15.7 以降をインストールします。

* ASP.NET Core アプリケーションで Entity Framework 2.1 を使用するには、ASP.NET Core 2.1 を使用します。 以前のバージョンの ASP.NET Core を使用するアプリケーションは 2.1 に更新する必要があります。

* Visual Studio 2015 は、.NET Framework 4.6.1 以降を対象とするアプリで使用することができます。 ただし .NET Standard 2.0 とその互換フレームワークを認識するバージョンの NuGet が必要です。 これを Visual Studio 2015 で取得するには、[NuGet クライアントをバージョン 3.6.0 にアップグレード](https://www.nuget.org/downloads)します。

## <a name="get-the-entity-framework-core-runtime"></a>Entity Framework Core のランタイムを入手する

EF Core のランタイム ライブラリをアプリケーションに追加するには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。 サポートされているプロバイダーとその NuGet パッケージ名の一覧は、「[データベース プロバイダー](../../providers/index.md)」を参照してください。

NuGet パッケージをインストールまたは更新するには、.NET Core CLI、Visual Studio パッケージ マネージャー ダイアログ、または Visual Studio パッケージ マネージャー コンソールを使用します。

ASP.NET Core 2.1 アプリケーションでは、メモリ内および SQL Server のプロバイダーが自動的に含まれるため、個別にインストールする必要はありません。

> [!TIP]  
> サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。 たとえば、前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.1 との間で互換性がありません。  

### <a name="net-core-cli"></a>.NET Core CLI

次の .NET Core CLI コマンドでは、SQL Server プロバイダーがインストールまたは更新されます。

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

`-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンを指定できます。 たとえば、EF Core 2.1.0 パッケージをインストールするには、コマンドに `-v 2.1.0` を追加します。

### <a name="visual-studio-nuget-package-manager-dialog"></a>Visual Studio の NuGet パッケージ マネージャー ダイアログ

* メニューから **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。

* **[参照]** または **[更新]** タブをクリックします。

* SQL Server プロバイダーをインストールまたは更新するには、`Microsoft.EntityFrameworkCore.SqlServer` パッケージを選択して確定します。

詳細については、「[NuGet パッケージ マネージャー UI](https://docs.microsoft.com/nuget/tools/package-manager-ui)」を参照してください。

### <a name="visual-studio-nuget-package-manager-console"></a>Visual Studio の NuGet パッケージ マネージャー コンソール

* メニューから **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します

* SQL Server プロバイダーをインストールするには、パッケージ マネージャー コンソールで、次のコマンドを実行します。

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* プロバイダーを更新するには、`Update-Package` コマンドを使用します。

* 特定のバージョンを指定するには、`-Version` 修飾子を使用します。 たとえば、EF Core 2.1.0 パッケージをインストールするには、コマンドに `-Version 2.1.0` を追加します

詳細については、「[パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/tools/package-manager-console)」を参照してください。

## <a name="get-entity-framework-core-tools"></a>Entity Framework Core ツールを入手する

設計時には、ランタイム ライブラリだけでなく、プロジェクトでいくつかの EF Core 関連タスクを実行できるツールをインストールできます。 たとえば、移行の作成、移行の適用、既存のデータベースに基づくモデルの作成などができます。

2 つのツールのセットを使用できます。
* .NET Core の[コマンド ライン インターフェイス (CLI) ツール](../../miscellaneous/cli/dotnet.md)は、Windows、Linux、または macOS で使用できます。 これらのコマンドは `dotnet ef` で始まります。 
* [パッケージ マネージャー コンソール ツール](../../miscellaneous/cli/powershell.md)は、Windows 上の Visual Studio 2017 で実行されます。 これらのコマンドは `Add-Migration`、`Update-Database` のように動詞で始まります。

パッケージ マネージャー コンソールの `dotnet ef` コマンドを使用できますが、Visual Studio を使用しているときにはパッケージ マネージャー コンソールのツールを使用する方が便利です。
* パッケージ マネージャー コンソールで現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。  
* コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。

<a name="cli"></a>

### <a name="get-the-cli-tools"></a>CLI ツールを入手する

.NET Core SDK には `dotnet ef` コマンドが含まれていますが、コマンドを有効にするには、`Microsoft.EntityFrameworkCore.Design` パッケージをインストールする必要があります。

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

ASP.NET Core 2.1 アプリでは、このパッケージが自動的に含まれています。

[必須コンポーネント](#prerequisites)で説明したように、.NET Core 2.1 SDK もインストールする必要があります。

> [!IMPORTANT]      
> ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。

### <a name="get-the-package-manager-console-tools"></a>パッケージ マネージャー コンソール ツールを入手する

EF Core のパッケージ マネージャー コンソール ツールを入手するには、`Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Tools
``` 

ASP.NET Core 2.1 アプリでは、このパッケージが自動的に含まれています。

## <a name="upgrading-to-ef-core-21"></a>EF Core 2.1 へのアップグレード

既存のアプリケーションを EF Core 2.1 にアップグレードするとき、場合によっては、古い EF Core パッケージの一部の参照を手動で削除する必要があります。

* データベース プロバイダーのデザイン時パッケージ (`Microsoft.EntityFrameworkCore.SqlServer.Design` など) は、EF Core 2.1 では不要になったか、またはサポートされなくなりましたが、他のパッケージをアップグレードする際に自動で削除されるわけではありません。

* .NET CLI ツールが .NET SDK に含まれるようになりました。このため、*.csproj* ファイルからパッケージへの参照を削除することができます。

  ```
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  ```

.NET Framework を対象とする、以前のバージョンの Visual Studio で作成されたアプリケーションの場合、.NET Standard 2.0 ライブラリと互換性があることを確認してください。

  * プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。

    ``` xml
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    ```

  * テスト プロジェクトの場合、次のエントリも必要です。

    ``` xml
    <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
    ```
