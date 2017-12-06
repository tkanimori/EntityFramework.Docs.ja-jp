---
title: "生成されたプロパティの EF コアの明示的な値の設定"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3f1993c2-cdf5-425b-bac2-a2665a20322b
ms.technology: entity-framework-core
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: f34e92d9a3b10b6ff904257ccd047a8acdaad231
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="setting-explicit-values-for-generated-properties"></a>生成されたプロパティの明示的な値の設定

生成されたプロパティは、(または使用して、EF データベース) の値が生成されるプロパティ、エンティティを追加または更新するときにします。 参照してください[生成プロパティ](../modeling/generated-properties.md)詳細についてはします。

生成されたことのではなく、生成されるプロパティの明示的な値を設定する場合があります。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/ExplicitValuesGenerateProperties/)GitHub でします。

## <a name="the-model"></a>モデル

この記事で使用されるモデルには、1 つが含まれている`Employee`エンティティです。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>追加中に、明示的な値を保存します。

`Employee.EmploymentStarted`プロパティ (既定値を使用して) 新しいエンティティをデータベースによって生成された値を持つように構成します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

次のコードは、データベースに 2 人の従業員を挿入します。
* 1 つ目の値が割り当てられていないに`Employee.EmploymentStarted`プロパティ、そのままとなりますので、CLR の既定値に設定`DateTime`です。
* 明示的な値を設定して、2 番目の`1-Jan-2000`します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

出力は、データベースの最初の従業員の値を生成して、明示的な値は、2 番目に使用されたことを示しています。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>SQL Server の ID 列に明示的な値

慣例により、`Employee.EmployeeId`プロパティは、ストアで生成された`IDENTITY`列です。

ほとんどの場合、キー プロパティの前に示したアプローチは機能しません。 ただし、SQL Server に明示的な値を挿入する`IDENTITY`列で、手動で有効にする必要があります`IDENTITY_INSERT`呼び出す前に`SaveChanges()`です。

> [!NOTE]  
> お、[機能要求](https://github.com/aspnet/EntityFramework/issues/703)自動的に行い、SQL Server プロバイダー内で、バックログにします。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

出力は、指定された id がデータベースに保存されたことを示しています。

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>更新中に、明示的な値の設定

`Employee.LastPayRaise`プロパティの更新中に、データベースによって生成された値を持つように構成します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> 既定では、EF コアは更新中に生成される構成されているプロパティの明示的な値を保存しようとする場合に例外をスローします。 これを回避するのには、下位レベルのメタデータ API にドロップダウンを設定する必要があります、 `AfterSaveBehavior` (上記のように)。

> [!NOTE]  
> **EF コア 2.0 での変更:**後保存動作の制御を通じて以前のリリースで、`IsReadOnlyAfterSave`フラグ。 このフラグを廃止され、置き換え`AfterSaveBehavior`です。

値を生成するデータベースでトリガーされても、`LastPayRaise`中に列`UPDATE`操作します。

[!code-sql[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

次のコードでは、データベース内の 2 つの従業員の給与が向上します。
* 1 つ目の値が割り当てられていないに`Employee.LastPayRaise`プロパティの設定は、null にします。
* 2 番目、1 週間前 (支払前までバック raise) の明示的な値が設定します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

出力は、データベースの最初の従業員の値を生成して、明示的な値は、2 番目に使用されたことを示しています。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
