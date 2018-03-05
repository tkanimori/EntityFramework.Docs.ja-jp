---
title: "同時実行の競合の EF コアの処理"
author: rowanmiller
ms.author: divega
ms.date: 03/03/2018
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: 288d9c6fced5ebbaa2c366248c68547502c3698e
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="handling-concurrency-conflicts"></a>同時実行の競合の処理

> [!NOTE]
> このページは、EF Core での同時実行制御のしくみと、アプリケーションで同時実行の競合を処理する方法を説明します。 参照してください[同時実行トークン](xref:core/modeling/concurrency)モデルの同時実行トークンを構成する方法の詳細。

> [!TIP]
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)は GitHub で確認できます。

_データベースの同時実行_状況で複数のプロセスまたはユーザーへのアクセスまたはデータベース内の同じデータを同時に変更を指します。 _同時実行制御_同時変更の有無でデータの一貫性を確保するために使用する特定のメカニズムを指します。

EF Core を実装する_オプティミスティック同時実行制御_ロックやことにより、複数のプロセスまたはユーザーの同期のオーバーヘッドなしとは独立して変更を加えることを意味します。 理想的な状況でこれらの変更は互いに影響しませんので、できるとを成功させるのにします。 最悪のシナリオで 2 つ以上のプロセスは、競合する変更を行うし、うち片方だけが成功する必要があります。

## <a name="how-concurrency-control-works-in-ef-core"></a>EF Core での同時実行制御のしくみ

同時実行トークンを使用してオプティミスティック同時実行制御を実装するように構成のプロパティ: 中に update または delete 操作が実行されるたびに`SaveChanges`元のに対して、データベースに対する同時実行トークンの値が比較EF コアによって読み取られた値。

- 値が一致した場合、操作が完了することができます。
- 値が一致しない場合、EF Core では、他のユーザーが競合する操作が実行して、現在のトランザクションを中止前提としています。

別のユーザーが現在の操作と競合する操作を実行すると、状況と呼ばれる_同時実行の競合_です。

データベース プロバイダーは、同時実行トークンの値の比較の実装を担当します。

リレーショナル データベースの EF コアにはで同時実行トークンの値については、チェックが含まれています、`WHERE`いずれかの句`UPDATE`または`DELETE`ステートメントです。 ステートメントを実行した後は、EF コアは、影響を受けた行の数を読み取ります。

行が影響はないと、同時実行の競合が検出されると、および EF コアをスロー`DbUpdateConcurrencyException`です。

構成することがありますたとえば、`LastName`で`Person`同時実行トークンであります。 ユーザーに任意の更新操作が 同時実行制御チェックが含まれます、`WHERE`句。

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>同時実行の競合を解決します。

引き続き使用する前の例では、1 人のユーザーが、いくつかの変更を保存しようとしています。、 `Person`、別のユーザーが既に変更されて、 `LastName` 、例外がスローされます。

この時点では、アプリケーションでしただけで更新が競合する変更のため失敗したことをユーザーに通知し、上に移動します。 このレコードがまだ同じの実際のユーザーを表すことを確認し、操作を再試行するユーザー入力を求めることが望ましい場合があります。

このプロセスの例は、_同時実行の競合を解決する_です。

同時実行の競合を解決するには、現在の保留中の変更をマージが含まれます`DbContext`値で、データベースにします。 どのような値を取得マージして、アプリケーションによって異なります、ユーザー入力から指示可能性があります。

**同時実行の競合を解決するために使用可能な値の 3 つのセットがあります。**

* **現在の値**値、アプリケーションがデータベースへの書き込みを試みたを示します。

* **元の値**はすべての編集が行われる前に、データベースから取得された元の値。

* **値をデータベース**はデータベースに格納されている値です。

同時実行の競合を処理する一般的な方法です。

1. キャッチ`DbUpdateConcurrencyException`中に`SaveChanges`です。
2. 使用して`DbUpdateConcurrencyException.Entries`影響を受けるエンティティの一連の変更を準備します。
3. データベースの現在の値を反映するように、同時実行トークンの元の値を更新します。
4. 競合が発生しないまでは、プロセスを再試行してください。

次の例では、`Person.FirstName`と`Person.LastName`は、同時実行トークンとしてセットアップします。 `// TODO:`を保存する値を選択するアプリケーション固有のロジックを含める場所にコメントです。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
