---
title: Microsoft SQL Server データベースプロバイダー-インデックス-EF Core
description: Entity Framework Core SQL Server プロバイダーに固有のインデックス機能
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129162"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Entity Framework Core SQL Server プロバイダーに固有のインデックス機能

このページでは、SQL Server プロバイダーに固有のインデックス構成オプションについて詳しく説明します。

## <a name="clustering"></a>クラスタリング

クラスター化インデックスは、テーブルまたはビュー内のデータ行をそのキー値に基づいて並べ替え、格納します。 テーブルに適切なクラスター化インデックスを作成すると、データは既に最適な順序でレイアウトされるため、クエリの速度が大幅に向上します。 データ行自体は 1 つの順序でしか並べ替えられないので、1 つのテーブルに設定できるクラスター化インデックスは 1 つだけです。 詳細については、 [クラスター化インデックスと非クラスター化インデックスに関する SQL Server のドキュメント](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)を参照してください。

既定では、テーブルの主キー列はクラスター化インデックスによって暗黙的にバックアップされ、他のすべてのインデックスは非クラスター化されます。

次のようにクラスター化するインデックスまたはキーを構成できます。

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> SQL Server はテーブルごとに1つのクラスター化インデックスのみをサポートし、主キーは既定でクラスター化されます。 非キー列にクラスター化インデックスを作成する場合は、明示的にキーを非クラスター化する必要があります。

## <a name="fill-factor"></a>FILL FACTOR

> [!NOTE]
> この機能は EF Core 5.0 で導入されました。

インデックスのデータストレージとパフォーマンスを微調整するために、インデックスの fill factor オプションが用意されています。 詳細については、 [FILL FACTOR に関する SQL Server のドキュメント](/sql/relational-databases/indexes/specify-fill-factor-for-an-index)を参照してください。

インデックスの FILL FACTOR は次のように構成できます。

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>オンライン作成

[オンライン] オプションを使用すると、インデックスの作成中に、基になるテーブルまたはクラスター化インデックスデータ、および関連する非クラスター化インデックスへの同時ユーザーアクセスが可能になり、基になるデータの更新やクエリをユーザーが続行できるようになります。 クラスター化インデックスの構築や再構築などのデータ定義言語 (DDL) 操作をオフラインで実行するときは、これらの操作により、基になるデータや関連付けられたインデックスに排他ロックがかけられます。 詳細については、 [オンラインインデックスオプションの SQL Server に関するドキュメント](/sql/relational-databases/indexes/perform-index-operations-online)を参照してください。

オンラインオプションを使用してインデックスを構成するには、次のようにします。

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
