---
title: 同時実行の競合の処理 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 2d8909585201a45eb020537847800f125b3b0120
ms.sourcegitcommit: 72e59e6af86b568653e1b29727529dfd7f65d312
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "42447716"
---
# <a name="handling-concurrency-conflicts"></a>同時実行の競合の処理

> [!NOTE]
> このページは、EF Core での同時実行のしくみと、アプリケーションで同時実行の競合を処理する方法について説明します。 モデルで同時実行トークンを構成する方法の詳細については、[同時実行トークン](xref:core/modeling/concurrency)に関するページを参照してください。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)は GitHub で確認できます。

"_データベースの同時実行_" とは、複数のプロセスまたはユーザーが、データベース内の同じデータに対して同時にアクセスするか、または変更を加える状況のことです。 "_同時実行の制御_" とは、同時実行の変更においてデータの一貫性を保証するために使用される特定のメカニズムのことです。

EF Core は、"_オプティミスティック同時実行制御_" を実装しています。これは、同期やロックのオーバーヘッドを伴わずに、複数のプロセスまたはユーザーが独立して変更を加えられることを意味します。 理想的な状況であれば、これらの変更が互いに干渉しないため、正常に処理できます。 最悪のケースのシナリオでは、2 つ以上のプロセスが、競合する変更を行おうとして、どちらか一方しか正常に処理されません。

## <a name="how-concurrency-control-works-in-ef-core"></a>EF Core での同時実行制御のしくみ

同時実行トークンとして構成されるプロパティは、オプティミスティック同時実行制御の実装に使用されます。更新または削除処理が `SaveChanges` の間に実行された場合は常に、データベース上の同時実行トークンの値が、EF Core で読み取られた元の値と比較されます。

- 値が一致した場合、操作は完了できます。
- 値が一致しない場合、EF Core では、別のユーザーが競合する処理を実行したと仮定して、現在のトランザクションを中断します。

現在の処理と競合する処理を別のユーザーが実行した場合の状況を、"_同時実行の競合_" といいます。

データベース プロバイダーは、同時実行トークン値の比較の実装を担います。

リレーショナル データベースでは、EF Core は `UPDATE` や `DELETE` ステートメントの `WHERE` 句に、同時実行トークン値のチェックを含みます。 ステートメントを実行した後、EF Core は、影響を受けた行数を読み取ります。

影響を受けた行がない場合、同時実行の競合が検出され、EF Core は `DbUpdateConcurrencyException` をスローします。

たとえば、`Person` 上で同時実行トークンになるように、`LastName` を構成できます。 これにより、Person 上での更新処理に、`WHERE` 句での同時実行のチェックが含まれるようになります。

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>同時実行の競合の解決

前の例に続けて、あるユーザーが `Person` に対する変更を保存しようとしたところ、別のユーザーが既に `LastName` を変更していた場合、例外がスローされます。

この時点で、アプリケーションは単純に、変更の競合が原因で更新が成功しなかったことをユーザーに通知して、次の処理へ移ります。 しかし、このレコードが引き続き実際の同一人物を表しており、処理を再試行できることを確認するよう、ユーザーに求めることが望ましい場合があります。

このプロセスは、"_同時実行の競合の解決_" の例です。

同時実行の競合を解決するには、現在の `DbContext` からの保留中の変更をデータベースの値とマージします。 どの値がマージされるかは、アプリケーションに基づいて変わります。また、ユーザー入力によって指示することが可能です。

**同時実行の競合を解決するために使用可能な 3 つの設定値を次に示します。**

* **現在の値**は、アプリケーションがデータベースへの書き込みを試行している値です。

* **元の値**は、何らかの編集が行われる前に、データベースから取得された元の値です。

* **データベース値**は、データベースに現在格納されている値です。

同時実行の競合を処理する一般的な方法は、次のとおりです。

1. `SaveChanges` の間に `DbUpdateConcurrencyException` をキャッチします。
2. `DbUpdateConcurrencyException.Entries` を使用して、影響を受けるエンティティに対する新しい変更の設定を準備します。
3. 同時実行トークンの元の値を更新して、データベースの現在の値を反映します。
4. 競合が発生しなくなるまで、プロセスを再試行します。

次の例では、`Person.FirstName` と `Person.LastName` は、同時実行トークンとして設定されています。 保存する値を選択するためのアプリケーション固有のロジックを含める場所に、`// TODO:` コメントがあります。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
