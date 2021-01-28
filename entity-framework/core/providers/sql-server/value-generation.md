---
title: Microsoft SQL Server データベースプロバイダー-値の生成-EF Core
description: SQL Server Entity Framework Core データベースプロバイダーに固有の値生成パターン
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987138"
---
# <a name="sql-server-value-generation"></a>SQL Server 値の生成

このページでは、SQL Server プロバイダーに固有の値の生成構成とパターンについて詳しく説明します。 最初に [ [値の生成] の [全般] ページ](xref:core/modeling/generated-properties)を読むことをお勧めします。

## <a name="identity-columns"></a>IDENTITY 列

規則により、追加で生成される値を持つように構成されている数値列は [SQL SERVER id 列](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)として設定されます。

### <a name="seed-and-increment"></a>シードと増分

既定では、ID 列は 1 (シード) から開始され、行が追加されるたびに1ずつ増加します (インクリメント)。 次のように、異なるシードと増分を構成できます。

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> ID シードと増分値を構成する機能は EF Core 3.0 で導入されました。

### <a name="inserting-explicit-values-into-identity-columns"></a>ID 列への明示的な値の挿入

既定では、SQL Server では、明示的な値を ID 列に挿入することはできません。 これを行うには `IDENTITY_INSERT` 、を呼び出す前に、次のようにを手動で有効にする必要があり `SaveChanges()` ます。

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> SQL Server プロバイダー内で自動的にこれを行うための[機能要求](https://github.com/aspnet/EntityFramework/issues/703)が、バックログに用意されています。

## <a name="sequences"></a>シーケンス

ID 列の代わりに、標準シーケンスを使用することもできます。 これは、さまざまなシナリオで役に立ちます。たとえば、複数の列で1つのシーケンスから既定値を描画することができます。

SQL Server を使用すると、シーケンスを作成し、 [シーケンスの [全般] ページ](xref:core/modeling/sequences)で詳しく説明されているように使用できます。 でシーケンスを使用するようにプロパティを構成する必要が `HasDefaultValueSql()` あります。
