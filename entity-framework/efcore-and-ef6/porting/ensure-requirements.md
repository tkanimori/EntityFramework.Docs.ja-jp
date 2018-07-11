---
title: EF6 から EF Core - への移植要件の検証
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 65bdc8bb9574d37db697aa47c8e8c480cefcb4f7
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949115"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a>EF6 から EF Core へ移植する前に: アプリケーションの要件の検証

移植プロセスを開始する前に、EF Core が、アプリケーションのデータ アクセス要件を満たしていることを検証する必要があります。

## <a name="missing-features"></a>不足している機能

EF Core が、アプリケーションで使用する必要がありますすべての機能を持つことを確認します。 参照してください[機能の比較](../features.md)EF Core での機能を EF6 を比較する方法の詳細な比較。 任意の必要な機能が存在しない場合は、EF Core に移植する前にこれらの機能の欠落を補うことができますを確認します。

## <a name="behavior-changes"></a>動作の変更

これは、EF6 と EF Core 間における動作のいくつかの変更の一部の一覧です。 留意してポートとして、アプリケーション、アプリケーションの動作は表示されませんコンパイル エラーとしてスワップすると、EF Core への方法が変わる可能性がある重要です。

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/Attach とグラフの動作

EF6 では、呼び出す`DbSet.Add()`ナビゲーション プロパティで参照されているすべてのエンティティの再帰的な検索でエンティティの結果にします。 検出され、コンテキストによって既に追跡されていない任意のエンティティも追加としてマークされます。 `DbSet.Attach()` すべてのエンティティがマークされている点を除いて同じですが、動作に変更されません。

**EF Core と同様の再帰検索を実行しますが、いくつかの若干異なる規則。**

*  ルート エンティティが要求された状態では常に (追加`DbSet.Add`の変更と`DbSet.Attach`)。

*  **エンティティのナビゲーション プロパティの再帰検索中に存在します。**

    *  **エンティティの主キーが生成されるストアの場合**

        * 主キーが値に設定されていない場合は、状態に追加された設定されます。 主キーの値と見なされます「設定なし」プロパティの型の CLR の既定値が割り当てられている場合 (たとえば、`0`の`int`、`null`の`string`など。)。

        * 主キーが値に設定されている場合、状態が unchanged に設定します。

    *  主キーがデータベースで生成されたでない場合は、エンティティは、ルートと同じ状態に置かれます。

### <a name="code-first-database-initialization"></a>コードの最初のデータベースの初期化

**EF6 が、大量のマジックのデータベース接続の選択やデータベースの初期化を実行します。これらのルールのとおりです。**

* 構成がない場合、EF6 は SQL Express または LocalDb にデータベースを選択します。

* コンテキストと同じ名前で接続文字列が、アプリケーションの場合`App/Web.config`ファイルでは、この接続が使用されます。

* データベースが存在しない場合は作成されます。

* テーブル モデルからは、データベースに存在する場合は、現在のモデルのスキーマがデータベースに追加されます。 Migrations を有効にする場合は、データベースの作成が使用されます。

* 場合は、データベースが存在し、EF6 以前に作成した、スキーマ、スキーマを現在のモデルとの互換性をチェックします。 スキーマが作成されたため、モデルが変更された場合、例外がスローされます。

**このマジックのいずれかの EF Core は実行されません。**

* コードでは、データベース接続を明示的に構成する必要があります。

* 初期化は実行されません。 使用する必要があります`DbContext.Database.Migrate()`移行を適用する (または`DbContext.Database.EnsureCreated()`と`EnsureDeleted()`migrations を使用せず、データベースの作成/削除する)。

### <a name="code-first-table-naming-convention"></a>名前付け規則、コードの最初のテーブル

EF6 では、エンティティにマップされる既定のテーブル名を計算する複数形化サービスを介してエンティティ クラス名が実行されます。

EF Core の名前を使用して、`DbSet`エンティティが、派生コンテキストで公開されているプロパティ。 エンティティがない場合、`DbSet`し、プロパティ、クラス名を使用します。
