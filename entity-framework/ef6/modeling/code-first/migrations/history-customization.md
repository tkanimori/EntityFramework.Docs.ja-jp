---
title: EF6 移行履歴テーブルのカスタマイズ
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
caps.latest.revision: 3
ms.openlocfilehash: 3805d99be6d37d037096f5c5fb69fd30197c1e91
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122548"
---
# <a name="customizing-the-migrations-history-table"></a>移行履歴テーブルのカスタマイズ
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

> [!NOTE]
> この記事では、基本的なシナリオで Code First Migrations を使用する方法を理解する前提としています。 そうしないかどうかを読み取る必要があります[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)続行する前にします。

## <a name="what-is-migrations-history-table"></a>移行履歴テーブルとは何ですか。

移行履歴テーブルは、データベースに適用される移行の詳細を格納する Code First Migrations で使用するテーブルです。 既定で、データベース内のテーブルの名前は\_ \_MigrationHistory とそれが最初の移行は、データベースに適用するときに作成します。 Entity Framework 5 では、次の表は、アプリケーションは、Microsoft Sql Server データベースを使用する場合に、システム テーブルをでした。 ただし Entity Framework 6 でこれが変更され、移行履歴テーブルがシステム テーブルをマークされていません。

## <a name="why-customize-migrations-history-table"></a>移行履歴テーブルをカスタマイズする理由ですか。

Code First Migrations を単独で使用する移行履歴テーブルは、手動で変更することで移行を中断できます。 ただし、場合によって、既定の構成は適していません、テーブルは、たとえば、カスタマイズする必要があります。

-   名前/3 を有効にする列のファセットを変更する必要があります<sup>rd</sup>パーティの移行
-   テーブルの名前を変更します。
-   既定以外のスキーマを使用する必要がある、 \_ \_MigrationHistory テーブル
-   コンテキストの特定のバージョンの追加のデータを格納する必要があると、そのため、テーブルに追加の列を追加する必要があります。

## <a name="words-of-precaution"></a>念のための単語

移行履歴テーブルを変更するには強力ですが、こうした過剰反応しないように注意する必要があります。 EF ランタイム現在はチェックしません、カスタマイズされた移行履歴テーブルが、ランタイムと互換性のあるかどうか。 アプリケーションではない場合は、予測できない方法で動作または実行時に中断された可能性があります。 これよりも使用が重要ですデータベースごとの複数のコンテキストを使用する場合である場合複数のコンテキストことができます、同じ移行履歴テーブルの移行に関する情報を格納します。

## <a name="how-to-customize-migrations-history-table"></a>移行履歴テーブルをカスタマイズする方法でしょうか。

開始する前に知っておくべき最初の移行を適用する前にのみ、移行履歴テーブルをカスタマイズできます。 ここでは、コードにします。

まず、System.Data.Entity.Migrations.History.HistoryContext クラスから派生したクラスを作成する必要があります。 Fluent API での EF モデルの構成とよく似ています、移行履歴テーブルを構成するため、HistoryContext クラスは、DbContext クラスから派生します。 同じ OnModelCreating メソッドをオーバーライドし、fluent API を使用して、テーブルを構成する必要があります。

>[!NOTE]
> 通常、EF モデルを構成するときに、呼び出しに必要はありません。以降、DbContext.OnModelCreating() オーバーライド OnModelCreating メソッドから OnModelCreating() が空の本文です。 移行履歴テーブルを構成するときにない場合になります。 この場合、最初に実際に呼び出す基本 OnModelCreating() オーバーライドではすべきです。OnModelCreating() します。 これは、オーバーライド元のメソッドで調整しする既定の方法での移行履歴テーブルを構成します。

たとえば、移行履歴テーブルの名前を変更し、"admin"と呼ばれるカスタム スキーマを配置したいとします。 さらに、DBA ほしいへの移行から MigrationId 列の名前を変更する\_id。  次に HistoryContext から派生したクラスを作成してこれを実現する可能性があります。

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

経由で登録することで EF を意識する必要がある、カスタム HistoryContext 準備ができたら[コード ベースの構成](http://msdn.com/data/jj680699):

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

これでほぼ完了です。 パッケージ マネージャー コンソール、Enable-migrations に移動できるようになりました Add-migration と最後にデータベースを更新します。 これは、HistoryContext 派生クラスで指定した詳細に従って移行履歴テーブルが構成されているデータベースに追加するのになります。

![データベース](~/ef6/media/database.png)
