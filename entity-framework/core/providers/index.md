---
title: "データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/providers/index
ms.openlocfilehash: 19c275b7e89c62e79c8bded977e39b2cfb2b439a
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="database-providers"></a>データベース プロバイダー

Entity Framework Core ではプロバイダー モデルを使用することで、さまざまなデータベースへのアクセスに EF を利用できます。 いくつかの概念はほとんどのデータベースに共通しており、プライマリ EF Core コンポーネントに含まれています。 そのような概念には、LINQ のクエリの表現、トランザクション、データベースから読み込んだときのオブジェクトの変更追跡などがあります。 いくつかの概念は、特定のプロバイダーに固有のものです。 たとえば、SQL Server プロバイダーの場合、メモリが最適化されたテーブル (SQL Server 固有の機能) を構成できます。 その他の概念は、プロバイダーのクラスに固有のものです。 たとえば、リレーショナル データベースの EF Core プロバイダーの場合、一般的な `Microsoft.EntityFrameworkCore.Relational` ライブラリに基づいてビルドします。このライブラリは、テーブルと列のマッピングや外部キー制約などを構成するための API を提供します。

EF Core プロバイダーは、さまざまなソースによってビルドされます。 一部のプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。
