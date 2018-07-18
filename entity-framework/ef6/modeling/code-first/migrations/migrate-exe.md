---
title: Migrate.exe - EF6 を使用します。
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
caps.latest.revision: 3
ms.openlocfilehash: 3a9bc47d2c18abe6ca70b92a8758b4d95010a464
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121868"
---
# <a name="using-migrateexe"></a>Migrate.exe を使用します。
Code First Migrations を使用してからデータベースを更新すること、visual studio 内でコマンド ライン ツールの migrate.exe 経由で実行することもできますが、します。 このページ migrate.exe を使用して、データベースに対して移行を実行する方法の概要を簡単になります。

> [!NOTE]
> この記事では、基本的なシナリオで Code First Migrations を使用する方法を理解する前提としています。 そうしないかどうかを読み取る必要があります[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)続行する前にします。

## <a name="copy-migrateexe"></a>コピー migrate.exe

Entity Framework をインストールするときに NuGet を使用してこの migrate.exe はダウンロードしたパッケージの [ツール] フォルダー内になります。 &lt;プロジェクト フォルダー&gt;\\パッケージ\\EntityFramework&lt; 。バージョン&gt;\\ツール

Migrate.exe をした後は、移行を含むアセンブリの場所にコピーする必要があります。

アプリケーションが .NET 4 を対象し、しない 4.5 し、コピーする必要が場合、 **Redirect.config**の場所にし名前を変更して**migrate.exe.config**します。これは、migrate.exe Entity Framework のアセンブリを検索できる正しいバインド リダイレクトを取得するようにします。

| .NET 4.5                                   | .NET 4.0                                   |
|:-------------------------------------------|:-------------------------------------------|
| ![Net45Files](~/ef6/media/net45files.png)  | ![Net40Files](~/ef6/media/net40files.png)  |

> [!NOTE]
> x64 をサポートしていない migrate.exe アセンブリ。

## <a name="using-migrateexe"></a>Migrate.exe を使用します。

移動すると migrate.exe 正しいフォルダーに、データベースに対して移行の実行に使用できる必要があります。 ユーティリティが実行するように設計は、移行を実行するだけです。 移行を生成または SQL スクリプトを作成できません。

### <a name="see-options"></a>オプションを参照してください。

``` console
Migrate.exe /?
```

上記はこのユーティリティでは、これが機能するために migrate.exe を実行しているのと同じ場所で、EntityFramework.dll する必要がありますに注意してくださいに関連付けられているヘルプ ページを表示します。

### <a name="migrate-to-the-latest-migration"></a>最新の移行への移行します。

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile=”..\\web.config”
```

構成ファイルを指定しない場合と migrate.exe に唯一の必須のパラメーターを実行することは、これを実行しようとしている移行を含むアセンブリには、アセンブリが、すべての規則が使用されますベースの設定をします。

### <a name="migrate-to-a-specific-migration"></a>特定の移行への移行します。

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /targetMigration=”AddTitle”
```

特定の移行までの移行を実行する場合は、移行の名前を指定できます。 これには、必要に応じて以前のすべての移行は実行まで、指定された移行を取得します。

### <a name="specify-working-directory"></a>作業ディレクトリを指定します。

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /startupDirectory=”c:\\MyApp”
```

アセンブリが依存関係を持っているか、作業ディレクトリに対して相対的なファイルを読み取る場合は、startupDirectory を設定する必要があります。

### <a name="specify-migration-configuration-to-use"></a>使用する移行の構成を指定します。

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile=”..\\web.config”
```

移行構成の複数のクラスがあれば、DbMigrationConfiguration から継承するクラスを指定する必要はこの実行に使用します。 これは、省略可能な 2 番目のパラメーターとして上のスイッチを使用せずに提供することによって指定されます。

### <a name="provide-connection-string"></a>接続文字列を指定します。

``` console
Migrate.exe BlogDemo.dll /connectionString=”Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI” /connectionProviderName=”System.Data.SqlClient”
```

コマンドラインでの接続文字列を指定する場合は、プロバイダー名も指定してください。 プロバイダー名を指定しないと、例外が発生します。

## <a name="common-problems"></a>一般的な問題

| エラー メッセージ                                                                                                                                                                                                                                                                                                                      | ソリューション                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未処理の例外: System.IO.FileLoadException: ファイルまたはアセンブリを読み込むことができません ' EntityFramework、バージョン, culture = neutral, PublicKeyToken = b77a5c561934e089' またはその依存関係の 1 つ。 指定したアセンブリのマニフェスト定義では、アセンブリ参照は一致しません。 (HRESULT からの例外: 0x80131040)         | 通常、つまり、Redirect.config ファイルを使用せず、.NET 4 アプリケーションを実行していること。 Redirect.config を migrate.exe と同じ場所にコピーして migrate.exe.config に名前を変更する必要があります。                                                                                       |
| 未処理の例外: System.IO.FileLoadException: ファイルまたはアセンブリを読み込むことができません ' EntityFramework、バージョン 4.4.0.0、カルチャを = = neutral, PublicKeyToken = b77a5c561934e089' またはその依存関係の 1 つ。 指定したアセンブリのマニフェスト定義では、アセンブリ参照は一致しません。 (HRESULT からの例外: 0x80131040)          | この例外は、.NET 4.5 が migrate.exe 場所にコピーされます、Redirect.config でアプリケーションを実行していることを意味します。 アプリが .NET 4.5 の場合は、構成ファイル内でリダイレクトする必要はありません。 Migrate.exe.config ファイルを削除します。                                    |
| エラー: 保留中の変更があるし、自動移行が無効になっているため、現在のモデルに合わせてデータベースを更新できません。 コード ベースの移行を保留中のモデルの変更を書き込むか、自動移行を有効にします。 自動移行の有効化する場合は true を DbMigrationsConfiguration.AutomaticMigrationsEnabled を設定します。 | このエラーは、実行されている移行、モデルに加えられた変更に対処するための移行を作成するいないし、データベースが、モデルと一致しない場合に発生します。 データベースをアップグレードへの移行を作成せず migrate.exe を実行し、モデル クラスにプロパティを追加するこの例に示します。 |
| エラー: メンバーの型は解決されません ' System.Data.Entity.Migrations.Design.ToolingFacade+UpdateRunner,EntityFramework、バージョン, culture = neutral, PublicKeyToken = b77a5c561934e089'。                                                                                                                                       | このエラーは、不適切なスタートアップ ディレクトリを指定することによって発生することができます。 Migrate.exe の場所があります。                                                                                                                                                                                      |
| 未処理の例外: System.NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません。 <br/>   System.Data.Entity.Migrations.Console.Program.Main (string[] args) に                                                                                                                                             | これは、使用しているシナリオに必要なパラメーターを指定しないことにより発生できます。 たとえば、プロバイダー名を指定せず、接続文字列を指定します。                                                                                                                        |
| エラー: 'ClassLibrary1' アセンブリには、複数の移行の構成の種類が見つかりました。 使用する 1 つの名前を指定します。                                                                                                                                                                                                  | 1 つ以上の構成クラスが、エラーが示すよう、指定したアセンブリであります。 /ConfigurationType スイッチを使用して、利用するかを指定する必要があります。                                                                                                                                           |
| エラー: ファイルまたはアセンブリに読み込むことができません '&lt;assemblyName&gt;' またはその依存関係の 1 つ。 指定したアセンブリ名またはコードベースが無効です。 (HRESULT からの例外: 0x80131047)                                                                                                                                                    | アセンブリ名の指定でない可能性があります。                                                                                                                                                                                                                          |
| エラー: ファイルまたはアセンブリに読み込むことができません '&lt;assemblyName&gt;' またはその依存関係の 1 つ。 正しくない形式のプログラムを読み込もうとしました。                                                                                                                                                                          | これは、x64 に対して migrate.exe を実行しようとしている場合アプリケーション。 EF 5.0 上下 x86 上でのみ機能します。                                                                                                                                                                                |
