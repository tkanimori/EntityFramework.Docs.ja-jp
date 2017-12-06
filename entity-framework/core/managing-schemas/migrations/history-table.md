---
title: "カスタムの移行履歴テーブルな EF コア"
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: cb9892241f3d7f1fae6293bd60a8a5c3e7120969
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="custom-migrations-history-table"></a>カスタムの移行履歴テーブル
===============================
既定では、EF コアの追跡でという名前のテーブルに記録して、移行の種類をデータベースに適用されている`__EFMigrationsHistory`です。 さまざまな理由で、ニーズに合わせて次の表をカスタマイズする場合があります。

> [!IMPORTANT]
> 移行履歴テーブルをカスタマイズする場合は*後*移行を適用する必要があります、データベースの既存のテーブルを更新します。

<a name="schema-and-table-name"></a>スキーマとテーブル名
----------------------
スキーマとを使用してテーブル名を変更することができます、`MigrationsHistoryTable()`メソッド`OnConfiguring()`(または`ConfigureServices()`ASP.NET Core 上)。 SQL Server の EF コア プロバイダーを使用する例を次に示します。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a>その他の変更
-------------
テーブルの追加の側面を構成するのに上書きし、プロバイダー固有の置換`IHistoryRepository`サービス。 MigrationId 列名を変更する例を次に示します*Id* SQL サーバーにします。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> `SqlServerHistoryRepository`内部の名前空間内部にあるし、将来のリリースで変更可能性があります。

``` csharp
class MyHistoryRepository : SqlServerHistoryRepository
{
    public MyHistoryRepository(HistoryRepositoryDependencies dependencies)
        : base(dependencies)
    {
    }

    protected override void ConfigureTable(EntityTypeBuilder<HistoryRow> history)
    {
        base.ConfigureTable(history);

        history.Property(h => h.MigrationId).HasColumnName("Id");
    }
}
```
