---
title: SQLite データベースプロバイダー-制限事項-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672779"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core データベース プロバイダーの制限事項

SQLite プロバイダーには、多くの移行に関する制限があります。 これらの制限事項のほとんどは、基になる SQLite データベースエンジンの制限の結果であり、EF に固有のものではありません。

## <a name="modeling-limitations"></a>モデリングの制限事項

一般的なリレーショナルライブラリ (Entity Framework リレーショナルデータベースプロバイダーによって共有されます) は、ほとんどのリレーショナルデータベースエンジンに共通のモデリング概念用の Api を定義します。 これらの概念のいくつかは、SQLite プロバイダーでサポートされていません。

* スキーマ
* シーケンス

## <a name="query-limitations"></a>クエリの制限事項

SQLite では、次のデータ型はネイティブでサポートされていません。 これらの型の値の読み取りと書き込みを行うことができる EF Core、等値 () のクエリ `where e.Property == value` もサポートされています。 ただし、比較や順序付けなどの操作では、クライアントでの評価が必要になります。

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

で `DateTimeOffset` はなく、DateTime 値を使用することをお勧めします。 複数のタイムゾーンを処理する場合は、保存してから適切なタイムゾーンに変換する前に、値を UTC に変換することをお勧めします。

この `Decimal` 型は、高レベルの精度を提供します。 ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。 [値コンバーター](../../modeling/value-conversions.md)を使用して、クラスで10進数の使用を続けることができます。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>移行に関する制限事項

SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。 サポートされていない操作の1つを SQLite データベースに適用しようとすると、が `NotSupportedException` スローされます。

| Operation            | サポート対象かどうか | バージョンが必要です |
|:---------------------|:-----------|:-----------------|
| Table.addcolumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✔          | 2.2.2            |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔ (非 op)  | 2.0              |
| DropSchema           | ✔ (非 op)  | 2.0              |
| 挿入               | ✔          | 2.0              |
| 更新               | ✔          | 2.0              |
| 削除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>移行の制限の回避策

これらの制限の一部を回避するには、移行でコードを手動で記述して、テーブルの再構築を実行します。 テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。 これらの手順の一部を実行するには、メソッドを使用する必要があり `Sql(string)` ます。

詳細については、SQLite のドキュメントで[他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter)方法に関するドキュメントを参照してください。

今後、EF では、これらの操作の一部を、テーブルの再構築方法を使用してサポートすることができます。 [この機能は、GitHub プロジェクトで追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)できます。
