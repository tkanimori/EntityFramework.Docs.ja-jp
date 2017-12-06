---
title: "SQLite データベース プロバイダーの制限 - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
ms.technology: entity-framework-core
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 08a4b8c26a3678491d412b333a7415cb45d4231f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF コア データベース プロバイダーの制限事項

SQLite プロバイダーでは、いくつかの移行の制限があります。 これらの制限事項のほとんどは、基になる SQLite データベース エンジンの制限事項の結果、EF に限定されません。

## <a name="modeling-limitations"></a>モデリングの制限事項

(Entity Framework プロバイダーによって共有リレーショナル データベース) 共通のリレーショナル ライブラリは、モデリングの概念は、ほとんどのリレーショナル データベース エンジンに共通の Api を定義します。 これらの概念のいくつかは、SQLite プロバイダーによってサポートされていません。

* スキーマ
* シーケンス

## <a name="migrations-limitations"></a>移行の制限事項

SQLite データベース エンジンは、多数の他のリレーショナル データベースの過半数によってサポートされているスキーマの操作をサポートしていません。 SQLite データベースにサポートされていない操作の 1 つを適用しようとするかどうか、`NotSupportedException`がスローされます。

| 操作            | サポートされているか。 |
| -------------------- | ---------- |
| AddColumn            | ✔          |
| AddForeignKey        | ✗          |
| AddPrimaryKey        | ✗          |
| AddUniqueConstraint  | ✗          |
| AlterColumn          | ✗          |
| CreateIndex          | ✔          |
| CreateTable          | ✔          |
| DropColumn           | ✗          |
| DropForeignKey       | ✗          |
| DropIndex            | ✔          |
| DropPrimaryKey       | ✗          |
| DropTable            | ✔          |
| DropUniqueConstraint | ✗          |
| Renamecolumn です。         | ✗          |
| RenameIndex          | ✗          |
| RenameTable          | ✔          |

## <a name="migrations-limitations-workaround"></a>移行の制限の回避策

回避策をいくつかを手動でテーブルを実行する、移行でコードを記述してこれらの制限を再構築します。 テーブルを再構築では、既存のテーブルの名前を変更する、新しいテーブルを作成する、新しいテーブルにデータをコピーおよび古いテーブルを削除する必要があります。 使用する必要があります、`Sql(string)`メソッドを次の手順の一部を実行します。

参照してください[その他の種類のテーブルのスキーマ変更を行う](http://sqlite.org/lang_altertable.html#otheralter)詳細については、SQLite ドキュメント。

将来的に、EF 可能性がありますサポートこれらの操作のいくつかのアプローチを使用して、テーブルの再構築は背後でします。 実行できます[当社の GitHub プロジェクトでこの機能を追跡](https://github.com/aspnet/EntityFramework/issues/329)です。
