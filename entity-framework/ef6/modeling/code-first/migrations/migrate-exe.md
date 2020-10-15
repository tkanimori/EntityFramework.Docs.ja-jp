---
title: migrate.exe-EF6 の使用
description: Entity Framework 6 での migrate.exe の使用
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/migrate-exe
ms.openlocfilehash: f57f56f7e3fe876c7265526bf6541e3c9e91ca7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066370"
---
# <a name="using-migrateexe"></a>migrate.exe の使用
Code First Migrations は、visual studio 内からデータベースを更新するために使用できますが、コマンドラインツール migrate.exe を使用して実行することもできます。 このページでは、migrate.exe を使用してデータベースに対して移行を実行する方法の概要を簡単に説明します。

> [!NOTE]
> この記事では、基本的なシナリオでの Code First Migrations の使用方法を理解していることを前提としています。 そうでない場合は、続行する前に [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) を読む必要があります。

## <a name="copy-migrateexe"></a>migrate.exe のコピー

NuGet を使用して Entity Framework をインストールすると、ダウンロードしたパッケージの tools フォルダー内に migrate.exe が作成されます。 &lt;プロジェクトフォルダー &gt; \\ パッケージ \\ entityframework。 &lt;バージョン &gt; \\ ツール

migrate.exe したら、移行を含むアセンブリの場所にコピーする必要があります。

アプリケーションが4.5 ではなく .NET 4 を対象としている場合は、その場所にも **Redirect.config** をコピーし **migrate.exe.config**名前を変更する必要があります。これは、migrate.exe が Entity Framework アセンブリを検索できるように、正しいバインディングリダイレクトを取得できるようにするためです。

| .NET 4.5                                      | .NET 4.0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![.NET 4.5 ファイル](~/ef6/media/net45files.png) | ![.NET 4.0 ファイル](~/ef6/media/net40files.png) |

> [!NOTE]
> migrate.exe は x64 アセンブリをサポートしていません。

migrate.exe を適切なフォルダーに移動すると、そのフォルダーを使用してデータベースに対して移行を実行できるようになります。 すべてのユーティリティは、移行を実行するように設計されています。 移行を生成したり、SQL スクリプトを作成したりすることはできません。

## <a name="see-options"></a>オプションを表示する

``` console
Migrate.exe /?
```

上の例では、このユーティリティに関連付けられているヘルプページが表示されます。これが機能するには、migrate.exe 実行しているのと同じ場所に EntityFramework.dll が必要です。

## <a name="migrate-to-the-latest-migration"></a>最新の移行に移行する

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

migrate.exe 実行する場合、唯一の必須パラメーターは、実行しようとしている移行を含むアセンブリであるアセンブリですが、構成ファイルを指定しない場合は、規則ベースのすべての設定が使用されます。

## <a name="migrate-to-a-specific-migration"></a>特定の移行に移行する

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

特定の移行までの移行を実行する場合は、移行の名前を指定できます。 これにより、移行が指定されるまで、必要に応じて以前の移行がすべて実行されます。

## <a name="specify-working-directory"></a>作業ディレクトリの指定

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

アセンブリに依存関係がある場合、または作業ディレクトリを基準としてファイルを読み取る場合は、startupDirectory を設定する必要があります。

## <a name="specify-migration-configuration-to-use"></a>使用する移行構成の指定

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

複数の移行構成クラス (DbMigrationConfiguration から継承するクラス) がある場合は、この実行に使用するを指定する必要があります。 これは、上記のようにスイッチを指定せずに、省略可能な2番目のパラメーターを指定することで指定します。

## <a name="provide-connection-string"></a>接続文字列の指定

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

コマンドラインで接続文字列を指定する場合は、プロバイダー名も指定する必要があります。 プロバイダー名を指定しないと、例外が発生します。

## <a name="common-problems"></a>一般的な問題

| エラー メッセージ                                                                                                                                                                                                                                                                                                                      | 解決策                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ハンドルされない例外: FileLoadException: ファイルまたはアセンブリ ' EntityFramework、Version = 5.0.0.0、Culture = ニュートラル、PublicKeyToken = b77a5c561934e089 '、またはその依存関係の1つを読み込むことができませんでした。 見つかったアセンブリのマニフェスト定義がアセンブリ参照と一致しません。 (HRESULT からの例外: 0x80131040)         | これは、通常、Redirect.config ファイルを使用せずに .NET 4 アプリケーションを実行していることを意味します。 Redirect.config を migrate.exe と同じ場所にコピーし、名前を migrate.exe.config に変更する必要があります。                                                                                       |
| ハンドルされない例外: FileLoadException: ファイルまたはアセンブリ ' EntityFramework、Version = 4.4.0.0、Culture = ニュートラル、PublicKeyToken = b77a5c561934e089 '、またはその依存関係の1つを読み込むことができませんでした。 見つかったアセンブリのマニフェスト定義がアセンブリ参照と一致しません。 (HRESULT からの例外: 0x80131040)          | この例外は、Redirect.config が migrate.exe の場所にコピーされた .NET 4.5 アプリケーションを実行していることを意味します。 アプリが .NET 4.5 の場合、構成ファイルが内にリダイレクトされる必要はありません。 migrate.exe.config ファイルを削除します。                                    |
| エラー: 保留中の変更があり、自動移行が無効になっているため、現在のモデルに一致するようにデータベースを更新できません。 保留中のモデルの変更をコードベースの移行に書き込むか、自動移行を有効にします。 自動移行を有効にするには、DbMigrationsConfiguration を true に設定します。 | このエラーは、モデルに対して行われた変更に対処するために移行を作成していない場合に、データベースがモデルと一致しない場合に発生します。 モデルクラスにプロパティを追加し、データベースをアップグレードするための移行を作成せずに migrate.exe を実行する例を次に示します。 |
| エラー: メンバー ' UpdateRunner、EntityFramework、Version = 5.0.0.0、Culture = ニュートラル、PublicKeyToken = b77a5c561934e089 ' の型が解決されていません。このエラーが発生しました。                                                                                                                                       | このエラーは、無効なスタートアップディレクトリを指定することによって発生することがあります。 これは migrate.exe の場所である必要があります                                                                                                                                                                                      |
| 未処理の例外: NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません。 <br/>   at.......................................                                                                                                                                             | これは、使用しているシナリオに必要なパラメーターを指定していないことが原因である可能性があります。 たとえば、プロバイダー名を指定せずに接続文字列を指定します。                                                                                                                        |
| エラー: アセンブリ ' と ' で、複数の移行構成の種類が見つかりました。 使用する名前を指定します。                                                                                                                                                                                                  | エラー状態が発生すると、指定されたアセンブリに複数の構成クラスが存在します。 使用するを指定するには、/configurationType スイッチを使用する必要があります。                                                                                                                                           |
| エラー: ファイルまたはアセンブリ ' &lt; assemblyName &gt; ' またはその依存関係の1つを読み込むことができませんでした。 指定されたアセンブリ名またはコードベースが無効です。 (HRESULT からの例外: 0x80131047)                                                                                                                                                    | これは、アセンブリ名の指定に誤りがあるか、                                                                                                                                                                                                                          |
| エラー: ファイルまたはアセンブリ ' &lt; assemblyName &gt; ' またはその依存関係の1つを読み込むことができませんでした。 正しくない形式のプログラムを読み込もうとしました。                                                                                                                                                                          | これは、x64 アプリケーションに対して migrate.exe を実行しようとした場合に発生します。 EF 5.0 以降は、x86 でのみ機能します。                                                                                                                                                                                |
