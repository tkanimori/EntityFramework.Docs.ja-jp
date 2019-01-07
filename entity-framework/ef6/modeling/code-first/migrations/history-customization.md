---
title: EF6 移行履歴テーブルのカスタマイズ
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058748"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="d6c9c-102">移行履歴テーブルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="d6c9c-102">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="d6c9c-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="d6c9c-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="d6c9c-105">この記事では、基本的なシナリオで Code First Migrations を使用する方法を理解する前提としています。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="d6c9c-106">そうしないかどうかを読み取る必要があります[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)続行する前にします。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="d6c9c-107">移行履歴テーブルとは何ですか。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-107">What is Migrations History Table?</span></span>

<span data-ttu-id="d6c9c-108">移行履歴テーブルは、データベースに適用される移行の詳細を格納する Code First Migrations で使用するテーブルです。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-108">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="d6c9c-109">既定で、データベース内のテーブルの名前は\_ \_MigrationHistory とそれを作成、データベースに最初の移行を適用する場合。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-109">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="d6c9c-110">Entity Framework 5 では、次の表は、アプリケーションは、Microsoft Sql Server データベースを使用する場合に、システム テーブルをでした。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-110">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="d6c9c-111">ただし Entity Framework 6 でこれが変更され、移行履歴テーブルがシステム テーブルをマークされていません。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-111">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="d6c9c-112">移行履歴テーブルをカスタマイズする理由ですか。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-112">Why customize Migrations History Table?</span></span>

<span data-ttu-id="d6c9c-113">Code First Migrations を単独で使用する移行履歴テーブルは、手動で変更することで移行を中断できます。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-113">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="d6c9c-114">ただし、場合によって、既定の構成は適していません、テーブルは、たとえば、カスタマイズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-114">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="d6c9c-115">名前/3 を有効にする列のファセットを変更する必要があります<sup>rd</sup>パーティの移行</span><span class="sxs-lookup"><span data-stu-id="d6c9c-115">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="d6c9c-116">テーブルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-116">You want to change the name of the table</span></span>
-   <span data-ttu-id="d6c9c-117">既定以外のスキーマを使用する必要がある、 \_ \_MigrationHistory テーブル</span><span class="sxs-lookup"><span data-stu-id="d6c9c-117">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="d6c9c-118">コンテキストの特定のバージョンの追加のデータを格納する必要があると、そのため、テーブルに追加の列を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-118">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="d6c9c-119">念のための単語</span><span class="sxs-lookup"><span data-stu-id="d6c9c-119">Words of precaution</span></span>

<span data-ttu-id="d6c9c-120">移行履歴テーブルを変更するには強力ですが、こうした過剰反応しないように注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-120">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="d6c9c-121">EF ランタイム現在はチェックしません、カスタマイズされた移行履歴テーブルが、ランタイムと互換性のあるかどうか。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-121">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="d6c9c-122">アプリケーションではない場合は、予測できない方法で動作または実行時に中断された可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-122">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="d6c9c-123">これよりも使用が重要ですデータベースごとの複数のコンテキストを使用する場合である場合複数のコンテキストことができます、同じ移行履歴テーブルの移行に関する情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-123">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="d6c9c-124">移行履歴テーブルをカスタマイズする方法でしょうか。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-124">How to customize Migrations History Table?</span></span>

<span data-ttu-id="d6c9c-125">開始する前に知っておくべき最初の移行を適用する前にのみ、移行履歴テーブルをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-125">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="d6c9c-126">ここでは、コードにします。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-126">Now, to the code.</span></span>

<span data-ttu-id="d6c9c-127">まず、System.Data.Entity.Migrations.History.HistoryContext クラスから派生したクラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-127">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="d6c9c-128">Fluent API での EF モデルの構成とよく似ています、移行履歴テーブルを構成するため、HistoryContext クラスは、DbContext クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-128">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="d6c9c-129">同じ OnModelCreating メソッドをオーバーライドし、fluent API を使用して、テーブルを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-129">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="d6c9c-130">通常、EF モデルを構成するときに、呼び出しに必要はありません。以降、DbContext.OnModelCreating() オーバーライド OnModelCreating メソッドから OnModelCreating() が空の本文です。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-130">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="d6c9c-131">移行履歴テーブルを構成するときにない場合になります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-131">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="d6c9c-132">この場合、最初に実際に呼び出す基本 OnModelCreating() オーバーライドではすべきです。OnModelCreating() します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-132">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="d6c9c-133">これは、オーバーライド元のメソッドで調整しする既定の方法での移行履歴テーブルを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-133">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="d6c9c-134">たとえば、移行履歴テーブルの名前を変更し、"admin"と呼ばれるカスタム スキーマを配置したいとします。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-134">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="d6c9c-135">さらに、DBA ほしいへの移行から MigrationId 列の名前を変更する\_id。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-135">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="d6c9c-136"> 次に HistoryContext から派生したクラスを作成してこれを実現する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-136"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="d6c9c-137">経由で登録することで EF を意識する必要がある、カスタム HistoryContext 準備ができたら[コード ベースの構成](https://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="d6c9c-137">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="d6c9c-138">これでほぼ完了です。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-138">That’s pretty much it.</span></span> <span data-ttu-id="d6c9c-139">パッケージ マネージャー コンソール、Enable-migrations に移動できるようになりました Add-migration と最後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-139">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="d6c9c-140">これは、HistoryContext 派生クラスで指定した詳細に従って移行履歴テーブルが構成されているデータベースに追加するのになります。</span><span class="sxs-lookup"><span data-stu-id="d6c9c-140">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![データベース](~/ef6/media/database.png)
