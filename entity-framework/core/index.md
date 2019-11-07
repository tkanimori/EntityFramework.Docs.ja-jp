---
title: Entity Framework Core の概要 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e6127f775d6bbbdf81debf5519388fe252fe079d
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655621"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core は人気の Entity Framework データ アクセス テクノロジの軽量版であり、拡張性に優れ、[オープン ソース](https://github.com/aspnet/EntityFrameworkCore)で、プラットフォームに依存しません。

EF Core はオブジェクト リレーショナル マッパー (O/RM) として機能します。これにより、.NET 開発者は .NET オブジェクトを使用してデータベースを操作できます。通常は開発者が記述する必要があるほとんどのデータ アクセス コードが不要になります。

EF Core は多くのデータベース エンジンに対応しています。詳細については、「[Database Providers](providers/index.md)」(データベース プロバイダー) を参照してください。

## <a name="the-model"></a>モデル

EF Core では、データ アクセスはモデルを利用して実行されます。 モデルはエンティティ クラスと、データベースとのセッションを表すコンテキスト オブジェクトから構成されます。これにより、データのクエリと保存が可能になります。 詳細については、「[Creating a Model](modeling/index.md)」 (モデルの作成) を参照してください。

既存データベースからモデルを生成したり、自分のデータベースに合わせてモデルのコードを直接記述したり、[EF 移行](managing-schemas/migrations/index.md)を使ってモデルからデータベースを作成してから、モデルが時間の経過と共に変化するのに合わせてそれを進化させたりできます。

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>クエリ実行

エンティティ クラスのインスタンスは、統合言語クエリ (LINQ) を利用し、データベースから取得されます。 詳細については、「[Querying Data](querying/index.md)」 (データのクエリ) を参照してください。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>データの保存

データはエンティティ クラスのインスタンスを利用し、データベース内で作成、削除、変更されます。 詳細については、「[Saving Data](saving/index.md)」 (データの保存) を参照してください。

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>次の手順

入門チュートリアルについては、「[Entity Framework Core の概要](get-started/index.md)」を参照してください。
