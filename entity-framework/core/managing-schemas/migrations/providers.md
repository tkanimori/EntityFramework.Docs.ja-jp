---
title: 複数のプロバイダーを使用した移行-EF Core
description: Entity Framework Core で複数のデータベースプロバイダーを対象とする場合に、移行を使用してデータベーススキーマを管理する
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429781"
---
# <a name="migrations-with-multiple-providers"></a>複数のプロバイダーを使用した移行

[EF Core ツール](xref:core/cli/index)は、アクティブなプロバイダーの移行のみをスキャフォールディングします。 ただし、場合によっては、DbContext を使用して、複数のプロバイダー (Microsoft SQL Server や SQLite など) を使用することもできます。 これを処理するには、複数の移行セット (プロバイダーごとに1つ) を保持し、各モデルの変更に対してそれぞれに移行を追加します。

## <a name="using-multiple-context-types"></a>複数のコンテキスト型の使用

複数の移行セットを作成する方法の1つは、プロバイダーごとに1つの DbContext 型を使用することです。

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

新しい移行を追加するときに、コンテキストの種類を指定します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> 後続の移行では、最後のディレクトリの兄弟として作成されるため、出力ディレクトリを指定する必要はありません。

## <a name="using-one-context-type"></a>1つのコンテキスト型の使用

DbContext 型を1つ使用することもできます。 現時点では、移行を別のアセンブリに移動する必要があります。 プロジェクトの設定手順については [、「個別の移行プロジェクトの使用](xref:core/managing-schemas/migrations/projects) 」を参照してください。

> [!TIP]
> この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations) で確認できます。

EF Core 5.0 以降では、ツールからアプリに引数を渡すことができます。 これにより、ツールの実行時にプロジェクトを手動で変更しなくても済むように、ワークフローをより効率的にすることができます。

ここでは、 [汎用ホスト](/dotnet/core/extensions/generic-host)を使用する場合に適切に機能する1つのパターンを示します。

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

既定のホストビルダーはコマンドライン引数から構成を読み取るため、ツールの実行時にプロバイダーを指定できます。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> この `--` トークンは、 `dotnet ef` 後続のすべてのものを引数として処理し、オプションとして解析しないように指示します。 によって使用されていない追加 `dotnet ef` の引数は、アプリに転送されます。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> アプリの追加の引数を指定する機能が EF Core 5.0 で追加されました。 以前のバージョンを使用している場合は、代わりに環境変数で構成値を指定してください。
