---
title: 移行の適用-EF Core
description: Entity Framework Core を使用して、運用データベースと開発データベースにスキーマの移行を適用する方法
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429853"
---
# <a name="applying-migrations"></a>移行の適用

移行を追加したら、それらをデプロイしてデータベースに適用する必要があります。 これを行うためのさまざまな方法があります。運用環境に適しているものと、開発ライフサイクルのものがあります。

> [!NOTE]
> 配置戦略にかかわらず、実稼働データベースに適用する前に、生成された移行を常に検査してテストします。 移行では、列の名前を変更しようとしたとき、またはデータベースに適用するさまざまな理由で失敗する場合があります。

## <a name="sql-scripts"></a>SQL スクリプト

運用データベースへの移行を展開するには、SQL スクリプトを生成することをお勧めします。 この方法には、次のような利点があります。

* SQL スクリプトの精度を確認できます。これは、実稼働データベースにスキーマの変更を適用すると、データの損失を伴う可能性がある危険性の高い操作であるため、重要です。
* 場合によっては、実稼働データベースの特定のニーズに合わせてスクリプトをチューニングできます。
* SQL スクリプトは、配置テクノロジと組み合わせて使用することも、CI プロセスの一部として生成することもできます。
* SQL スクリプトは DBA に提供でき、個別に管理およびアーカイブできます。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>基本的な使用方法

次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>From を使用する (暗黙的な指定に対して)

次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>From および To を使用する

次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。

> [!WARNING]
> データ損失の可能性のあるシナリオには注意してください。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>基本的な使用方法

次の例では、空のデータベースから最新の移行に SQL スクリプトを生成します。

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>From を使用する (暗黙的な指定に対して)

次の例では、指定された移行から最新の移行までの SQL スクリプトを生成します。

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>From および To を使用する

次のは、指定された移行から指定された移行に SQL スクリプトを生成し `from` `to` ます。

```powershell
Script-Migration AddNewTables AddAuditTable
```

ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。 *データ損失の可能性のあるシナリオには注意してください。*

***

スクリプトの生成では、次の2つの引数を使用して、どの移行範囲を生成する必要があるかを示します。

* **from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。 移行が適用されていない場合、`0` を指定します (これは既定です)。
* **to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。 これは既定でプロジェクトの最後の移行になります。

## <a name="idempotent-sql-scripts"></a>べき等 SQL スクリプト

上記で生成した SQL スクリプトは、ある移行から別の移行にスキーマを変更する場合にのみ適用できます。スクリプトは適切に適用する必要があり、適切な移行状態のデータベースのみに適用することをお勧めします。 EF Core では、 **べき等** スクリプトの生成もサポートしています。このスクリプトでは、(移行履歴テーブルを使用して) 既に適用されている移行を内部で確認し、不足しているもののみを適用します これは、データベースに最後に適用された移行の内容を正確に把握していない場合、またはそれぞれが異なる移行に配置されている複数のデータベースに配置する場合に便利です。

次の例では、べき等移行が生成されます。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Script-Migration -Idempotent
```

**_

## <a name="command-line-tools"></a>コマンドライン ツール

EF コマンドラインツールを使用すると、データベースに移行を適用できます。 移行のローカル開発とテストの生産性を維持しながら、この方法は運用データベースの管理には適していません。

_ SQL コマンドはツールによって直接適用されるので、開発者はそれを検査または変更することはできません。 これは、運用環境では危険である可能性があります。
* .NET SDK と EF ツールは、実稼働サーバーにインストールする必要があります。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

次の例では、データベースを最新の移行に更新します。

```dotnetcli
dotnet ef database update
```

次の例では、指定された移行にデータベースを更新します。

```dotnetcli
dotnet ef database update AddNewTables
```

これは、以前の移行にもロールバックするために使用できることに注意してください。

> [!WARNING]
> データ損失の可能性のあるシナリオには注意してください。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

次の例では、データベースを最新の移行に更新します。

```powershell
Update-Database
```

次の例では、指定された移行にデータベースを更新します。

```powershell
Update-Database AddNewTables
```

これは、以前の移行にもロールバックするために使用できることに注意してください。

> [!WARNING]
> データ損失の可能性のあるシナリオには注意してください。

***

コマンドラインツールを使用した移行の適用の詳細については、「 [EF Core ツールリファレンス](xref:core/cli/index)」を参照してください。

## <a name="apply-migrations-at-runtime"></a>実行時に移行を適用する

アプリケーション自体が、通常は起動時にプログラムを使用して移行を適用できる可能性があります。 移行のローカル開発とテストの生産性を維持しながら、次のような理由から、この方法は実稼働データベースの管理には適していません。

* アプリケーションの複数のインスタンスが実行されている場合、両方のアプリケーションが移行を同時に適用して失敗する (または、データの破損の原因となる) ことがあります。
* 同様に、別のアプリケーションが移行している間にアプリケーションがデータベースにアクセスしている場合は、重大な問題が発生する可能性があります。
* データベーススキーマを変更するには、アプリケーションに昇格されたアクセス権が必要です。 一般に、運用環境でアプリケーションのデータベースのアクセス許可を制限することをお勧めします。
* 問題が発生した場合は、適用された移行をロールバックできることが重要です。 その他の戦略では、簡単にそのまま使用できます。
* SQL コマンドはプログラムによって直接適用されるので、開発者はそれを検査または変更することはできません。 これは、運用環境では危険である可能性があります。

プログラムによって移行を適用するには、を呼び出し `context.Database.Migrate()` ます。 たとえば、一般的な ASP.NET アプリケーションでは、次の操作を実行できます。

```csharp
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

は、 `Migrate()` `IMigrator` より高度なシナリオで使用できるサービスの上に構築されていることに注意してください。 アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。

> [!WARNING]
>
> * 実稼働環境でこの方法を使用する前に、慎重に検討してください。 この展開戦略の単純化は、作成される問題によって上回るされていることがわかりました。 代わりに、移行から SQL スクリプトを生成することを検討してください。
> * `Migrate()` の前に `EnsureCreated()` を呼び出さないでください。 `EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。
