---
title: SQLite データベースプロバイダー-制限事項-EF Core
description: 他のプロバイダーと比較した場合の Entity Framework Core SQLite データベースプロバイダーの制限事項
author: bricelam
ms.date: 07/16/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 546910afb9c97a93a7cc471bb813be0b9874a4bd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071226"
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

この `Decimal` 型は、高レベルの精度を提供します。 ただし、このレベルの精度が不要な場合は、代わりに double を使用することをお勧めします。 [値コンバーター](xref:core/modeling/value-conversions)を使用して、クラスで10進数の使用を続けることができます。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>移行に関する制限事項

SQLite データベースエンジンでは、他の多くのリレーショナルデータベースでサポートされているいくつかのスキーマ操作はサポートされていません。 サポートされていない操作の1つを SQLite データベースに適用しようとすると、が `NotSupportedException` スローされます。

特定の操作を実行するために再構築が試行されます。 再構築を行うことができるのは、EF Core モデルの一部であるデータベースアーティファクトだけです。 データベースアーティファクトがモデルの一部ではない場合 (たとえば、移行中に手動で作成された場合)、 `NotSupportedException` がまだスローされます。

| 操作            | サポート対象かどうか  | バージョンが必要です |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (再構築) | 5.0              |
| Table.addcolumn            | ✔           | 1.0              |
| AddForeignKey        | ✔ (再構築) | 5.0              |
| AddPrimaryKey        | ✔ (再構築) | 5.0              |
| AddUniqueConstraint  | ✔ (再構築) | 5.0              |
| AlterColumn          | ✔ (再構築) | 5.0              |
| CreateIndex          | ✔           | 1.0              |
| CreateTable          | ✔           | 1.0              |
| DropCheckConstraint  | ✔ (再構築) | 5.0              |
| DropColumn           | ✔ (再構築) | 5.0              |
| DropForeignKey       | ✔ (再構築) | 5.0              |
| DropIndex            | ✔           | 1.0              |
| DropPrimaryKey       | ✔ (再構築) | 5.0              |
| DropTable            | ✔           | 1.0              |
| DropUniqueConstraint | ✔ (再構築) | 5.0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔ (再構築) | 2.1              |
| RenameTable          | ✔           | 1.0              |
| EnsureSchema         | ✔ (非 op)   | 2.0              |
| DropSchema           | ✔ (非 op)   | 2.0              |
| 挿入               | ✔           | 2.0              |
| 更新               | ✔           | 2.0              |
| 削除               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>移行の制限の回避策

再構築を実行するために、移行でコードを手動で記述することで、これらの制限の一部を回避できます。 テーブルの再構築では、新しいテーブルを作成し、新しいテーブルにデータをコピーし、古いテーブルを削除して、新しいテーブルの名前を変更します。 これらの手順の一部を実行するには、メソッドを使用する必要があり `Sql(string)` ます。

詳細については、SQLite のドキュメントで [他の種類のテーブルスキーマ変更を行う](https://sqlite.org/lang_altertable.html#otheralter) 方法に関するドキュメントを参照してください。
