---
title: SQLite データベース プロバイダー - 制限事項 - EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 69c40fcd8b7ddb925728b1bad9992ad2a81e7540
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994665"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core データベース プロバイダーの制限事項

SQLite プロバイダーでは、いくつかの移行の制限があります。 これらの制限事項のほとんどは、基になる SQLite データベース エンジンの制限事項の結果し、EF に固有ではありません。

## <a name="modeling-limitations"></a>モデリングの制限事項

(Entity Framework のリレーショナル データベース プロバイダーによって共有)、一般的なリレーショナル ライブラリは、モデリングの概念はほとんどのリレーショナル データベース エンジンに共通の Api を定義します。 これらの概念のいくつかは、SQLite プロバイダーによってサポートされていません。

* スキーマ
* シーケンス

## <a name="migrations-limitations"></a>移行の制限事項

SQLite データベース エンジンは、多数の他のリレーショナル データベースの大部分でサポートされているスキーマの操作をサポートしていません。 サポートされていない操作のいずれかの SQLite データベースに適用しようとしたかどうか、`NotSupportedException`がスローされます。

| 操作            | サポートされていますか。 | バージョンが必要です。 |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1              |
| CreateTable          | ✔          | 1              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✗          |                  |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1              |
| EnsureSchema         | ✔ (操作なし)  | 2.0              |
| DropSchema           | ✔ (操作なし)  | 2.0              |
| 挿入               | ✔          | 2.0              |
| 更新               | ✔          | 2.0              |
| 削除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>移行の制限の回避策

回避策をいくつかは、移行テーブルを実行するコードを手動で記述してこれらの制限の再構築します。 テーブルの再構築では、既存のテーブルの名前を変更する、新しいテーブルを作成、新しいテーブルにデータをコピーおよび古いテーブルを削除する必要があります。 使用する必要があります、`Sql(string)`メソッドを次の手順の一部を実行します。

参照してください[その他の種類のテーブル スキーマ変更を行う](http://sqlite.org/lang_altertable.html#otheralter)詳細については、SQLite ドキュメント。

将来的に、EF がサポートこれらの操作の一部裏では、テーブルの再構築のアプローチを使用しています。 できます[当社の GitHub プロジェクトでこの機能を追跡](https://github.com/aspnet/EntityFrameworkCore/issues/329)します。
