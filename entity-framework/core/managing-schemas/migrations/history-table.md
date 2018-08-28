---
title: カスタムの移行履歴テーブル - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.openlocfilehash: 7ee76cadd6fac4ec403918e88460e43067ae5815
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995697"
---
<a name="custom-migrations-history-table"></a>カスタムの移行履歴テーブル
===============================
既定では、EF Core の追跡でという名前のテーブルに記録して、移行をデータベースに適用されている`__EFMigrationsHistory`します。 さまざまな理由から、ニーズに合うように、このテーブルをカスタマイズします。

> [!IMPORTANT]
> 移行履歴テーブルをカスタマイズする場合は*後*移行を適用する必要があります、データベースの既存のテーブルを更新します。

<a name="schema-and-table-name"></a>スキーマとテーブル名
----------------------
スキーマと名前を使用してテーブルを変更することができます、`MigrationsHistoryTable()`メソッド`OnConfiguring()`(または`ConfigureServices()`の ASP.NET core)。 SQL Server EF Core プロバイダーを使用する例を示します。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

<a name="other-changes"></a>その他の変更
-------------
表に、その他の側面を構成するには、オーバーライドおよびプロバイダーに固有の置換`IHistoryRepository`サービス。 MigrationId 列名を変更する例を次に示します*Id* SQL サーバーにします。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> `SqlServerHistoryRepository` 内部の名前空間内で、将来のリリースで変更可能性があります。

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
