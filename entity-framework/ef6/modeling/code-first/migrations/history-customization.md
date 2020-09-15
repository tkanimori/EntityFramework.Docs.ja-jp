---
title: 移行履歴テーブルのカスタマイズ-EF6
description: Entity Framework 6 で移行履歴テーブルをカスタマイズする
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: b71ac7635d6804af2db9bf00acf7e8f4b2a11033
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073462"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="bffd7-103">移行履歴テーブルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="bffd7-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="bffd7-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="bffd7-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="bffd7-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="bffd7-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="bffd7-106">この記事では、基本的なシナリオでの Code First Migrations の使用方法を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="bffd7-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="bffd7-107">そうでない場合は、続行する前に [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) を読む必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="bffd7-108">移行履歴テーブルとは</span><span class="sxs-lookup"><span data-stu-id="bffd7-108">What is Migrations History Table?</span></span>

<span data-ttu-id="bffd7-109">移行履歴テーブルは、データベースに適用された移行の詳細を格納するために Code First Migrations によって使用されるテーブルです。</span><span class="sxs-lookup"><span data-stu-id="bffd7-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="bffd7-110">既定では、データベース内のテーブルの名前は MigrationHistory で、 \_ \_ データベースへの最初の移行を適用するときに作成されます。</span><span class="sxs-lookup"><span data-stu-id="bffd7-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="bffd7-111">Entity Framework 5 では、アプリケーションが Microsoft Sql Server データベースを使用していた場合、このテーブルはシステムテーブルでした。</span><span class="sxs-lookup"><span data-stu-id="bffd7-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="bffd7-112">これは Entity Framework 6 で変更され、移行履歴テーブルはシステムテーブルとしてマークされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="bffd7-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="bffd7-113">移行履歴テーブルをカスタマイズする理由</span><span class="sxs-lookup"><span data-stu-id="bffd7-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="bffd7-114">移行履歴テーブルは Code First Migrations によってのみ使用され、手動で変更すると移行が中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="bffd7-115">ただし、既定の構成が適切ではなく、テーブルをカスタマイズする必要がある場合があります。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="bffd7-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="bffd7-116"><sup>サードパーティの</sup>移行プロバイダーを有効にするには、列の名前やファセットを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="bffd7-117">テーブルの名前を変更するには</span><span class="sxs-lookup"><span data-stu-id="bffd7-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="bffd7-118">\_MigrationHistory テーブルに既定以外のスキーマを使用する必要がある \_</span><span class="sxs-lookup"><span data-stu-id="bffd7-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="bffd7-119">コンテキストの特定のバージョンの追加データを格納する必要があるため、テーブルに列を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="bffd7-120">予防策</span><span class="sxs-lookup"><span data-stu-id="bffd7-120">Words of precaution</span></span>

<span data-ttu-id="bffd7-121">移行履歴テーブルを変更することは強力ですが、過剰な作業を行わないように注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="bffd7-122">EF runtime では、現在、カスタマイズされた移行履歴テーブルにランタイムとの互換性があるかどうかは確認されません。</span><span class="sxs-lookup"><span data-stu-id="bffd7-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="bffd7-123">そうでない場合は、実行時にアプリケーションが中断したり、予期しない動作が発生したりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="bffd7-124">これは、データベースごとに複数のコンテキストを使用する場合にさらに重要になります。この場合、複数のコンテキストで同じ移行履歴テーブルを使用して、移行に関する情報を格納できます。</span><span class="sxs-lookup"><span data-stu-id="bffd7-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="bffd7-125">移行履歴テーブルをカスタマイズする方法</span><span class="sxs-lookup"><span data-stu-id="bffd7-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="bffd7-126">開始する前に、最初の移行を適用する前にのみ、移行履歴テーブルをカスタマイズできることを把握しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="bffd7-127">ここで、コードにします。</span><span class="sxs-lookup"><span data-stu-id="bffd7-127">Now, to the code.</span></span>

<span data-ttu-id="bffd7-128">最初に、システムから派生したクラスを作成する必要があります。このクラスは、</span><span class="sxs-lookup"><span data-stu-id="bffd7-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="bffd7-129">履歴コンテキストクラスは DbContext クラスから派生するため、移行履歴テーブルを構成することは、fluent API での EF モデルの構成とよく似ています。</span><span class="sxs-lookup"><span data-stu-id="bffd7-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="bffd7-130">OnModelCreating メソッドをオーバーライドし、fluent API を使用してテーブルを構成するだけです。</span><span class="sxs-lookup"><span data-stu-id="bffd7-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="bffd7-131">通常、EF モデルを構成するときは、base を呼び出す必要はありません。DbContext. OnModelCreating () に空の本文があるため、オーバーライドされた OnModelCreating メソッドから OnModelCreating () が行われました。</span><span class="sxs-lookup"><span data-stu-id="bffd7-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="bffd7-132">これは、移行履歴テーブルを構成する場合には当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="bffd7-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="bffd7-133">この場合、OnModelCreating () オーバーライドで最初に行うことは、base を呼び出すことです。OnModelCreating ()。</span><span class="sxs-lookup"><span data-stu-id="bffd7-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="bffd7-134">これにより、移行履歴テーブルが既定の方法で構成されます。これにより、上書きする方法を調整できます。</span><span class="sxs-lookup"><span data-stu-id="bffd7-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="bffd7-135">たとえば、移行履歴テーブルの名前を変更し、"admin" というカスタムスキーマに格納するとします。</span><span class="sxs-lookup"><span data-stu-id="bffd7-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="bffd7-136">さらに、DBA は、MigrationId 列の名前を移行 ID に変更する必要が \_ あります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="bffd7-137">これを実現するには、履歴コンテキストから派生した次のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="bffd7-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="bffd7-138">カスタム履歴コンテキストを準備したら、 [コードベースの構成](https://msdn.com/data/jj680699)を使用して登録することにより、EF に認識させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="bffd7-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="bffd7-139">これは非常に簡単です。</span><span class="sxs-lookup"><span data-stu-id="bffd7-139">That’s pretty much it.</span></span> <span data-ttu-id="bffd7-140">これで、パッケージマネージャーコンソールに移動し、移行、追加、および更新を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="bffd7-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="bffd7-141">これにより、履歴コンテキストの派生クラスで指定した詳細に従って、構成された移行履歴テーブルがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bffd7-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![移行履歴テーブル](~/ef6/media/database.png)
