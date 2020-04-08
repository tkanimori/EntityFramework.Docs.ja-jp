---
title: 生成されるプロパティに明示的な値を設定する - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3f1993c2-cdf5-425b-bac2-a2665a20322b
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: 43c4ab3c2a60645cdeff2a6cc40ce979f832f2fd
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413631"
---
# <a name="setting-explicit-values-for-generated-properties"></a>生成されるプロパティに明示的な値を設定する

生成されるプロパティとは、エンティティが追加または更新されるときに、(EF またはデータベースのどちらかによって) 値が生成されるプロパティのことです。 詳細については、「[生成される値](../modeling/generated-properties.md)」を参照してください。

プロパティを生成するのではなく、生成されるプロパティに明示的な値を設定したいという場合があります。

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/)は GitHub で確認できます。

## <a name="the-model"></a>モデル

この記事で使用されるモデルには、単一の `Employee` エンティティが含まれます。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>追加中に明示的な値を保存する

`Employee.EmploymentStarted` プロパティは、(既定の設定を使用して) 新しいエンティティに対して、データベースで値が生成されるように構成されています。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

次のコードでは、データベースに 2 名の従業員を挿入しています。

* 1 人目の従業員では、`Employee.EmploymentStarted` プロパティに割り当てられている値がないため、`DateTime` には CLR 既定値が設定されたままです。
* 2 人目の従業員では、`1-Jan-2000` という明示的な値を設定しました。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

1 人目の従業員に対してはデータベースが値を生成したこと、また、2 人目の従業員に対しては明示的な値が使用されたことが、出力に表示されます。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>SQL Server の IDENTITY 列に対する明示的な値

慣例として、`Employee.EmployeeId` プロパティは、ストア生成された `IDENTITY` 列です。

ほとんどの状況で、上述した方法がキー プロパティに対して有効です。 しかし、SQL Server の `IDENTITY` 列に明示的な値を挿入するには、`IDENTITY_INSERT` を呼び出す前に `SaveChanges()` を手動で有効にする必要があります。

> [!NOTE]  
> SQL Server プロバイダー内で自動的にこれを行うための[機能要求](https://github.com/aspnet/EntityFramework/issues/703)が、バックログに用意されています。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

指定された ID がデータベースに保存されたことが、出力に表示されます。

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>更新中に明示的な値を設定する

`Employee.LastPayRaise` プロパティは、更新中にデータベースで値が生成されるように構成されています。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> 既定では、更新中に生成されるように構成されたプロパティに明示的な値の保存を試行しようとすると、EF Core は例外をスローします。 これを回避するには、下位レベルのメタデータ API にドロップダウンして、`AfterSaveBehavior` を設定する必要があります (上記を参照)。

> [!NOTE]  
> **EF Core 2.0 での変更:** 前のリリースでは、保存後の動作は `IsReadOnlyAfterSave` フラグ経由で制御されていました。 このフラグは廃止され、`AfterSaveBehavior` に置き換えられました。

また、データベースには、`LastPayRaise` 操作中に `UPDATE` 列の値を生成するためのトリガーがあります。

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

次のコードでは、データベース内の 2 人の従業員の給与を引き上げています。

* 1 人目の従業員では、`Employee.LastPayRaise` プロパティに割り当てられている値がないため、null に設定されたままです。
* 2 人目の従業員では、1 週間前 (給与引き上げの日付へ戻る) の明示的な値を設定します。

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

1 人目の従業員に対してはデータベースが値を生成したこと、また、2 人目の従業員に対しては明示的な値が使用されたことが、出力に表示されます。

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
