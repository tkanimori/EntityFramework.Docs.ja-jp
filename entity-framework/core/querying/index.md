---
title: データのクエリ - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
ms.technology: entity-framework-core
uid: core/querying/index
ms.openlocfilehash: a2dd830b25c64b007a881c105a87b5c631b00266
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="querying-data"></a>データのクエリ

Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。 LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。 LINQ クエリの表現がデータベース プロバイダーに渡され、データベース固有のクエリ言語 (たとえば、リレーショナル データベースの場合は SQL) に変換されます。 クエリが処理されるしくみについては、「[クエリのしくみ](overview.md)」を参照してください。
