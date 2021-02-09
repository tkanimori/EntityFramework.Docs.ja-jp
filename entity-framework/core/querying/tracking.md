---
title: 追跡と追跡なしのクエリ - EF Core
description: Entity Framework Core の追跡クエリと非追跡クエリに関する情報
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: cb18125fb3453bb533981afb36480b12727cd6f2
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983418"
---
# <a name="tracking-vs-no-tracking-queries"></a>追跡と追跡なしのクエリ

Entity Framework Core によってその変更トラッカー内のエンティティ インスタンスに関する情報が保持されるかどうかは、追跡動作によって制御されます。 エンティティが追跡されている場合、エンティティ内で検出された変更はすべて、`SaveChanges()` の間にデータベースに対して永続化されます。 また、EF Core により、追跡クエリの結果内のエンティティ、および変更トラッカーに含まれるエンティティの間のナビゲーション プロパティも修正されます。

> [!NOTE]
> [キーなしエンティティ型](xref:core/modeling/keyless-entity-types)は追跡されません。 この記事でエンティティ型に関する言及がある場合は、必ずキーが定義されているエンティティ型を参照しています。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking)は GitHub で確認できます。

## <a name="tracking-queries"></a>追跡クエリ

既定では、エンティティ型を返すクエリは、追跡を行います。 つまり、それらのエンティティ インスタンスに変更を加え、`SaveChanges()` によってその変更を永続化させることができます。 次の例では、ブログ評価に対する変更が検出され、`SaveChanges()` の間にデータベースに対して永続化されています。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

追跡クエリで結果が返されると、EF Core で、エンティティが既にコンテキスト内に存在するかどうかがチェックされます。 EF Core で既存のエンティティが検出された場合は、同じインスタンスが返されます。 EF Core では、エントリ内にあるエンティティのプロパティの現在の値と元の値がデータベースの値で上書きされません。 エンティティがコンテキスト内に見つからない場合は、EF Core で新しいエンティティ インスタンスが作成され、コンテキストにアタッチされます。 クエリ結果には、エンティティが含まれません。エンティティはコンテキストに追加されますが、まだデータベースには保存されていません。

## <a name="no-tracking-queries"></a>追跡なしのクエリ

追跡なしのクエリは、読み取り専用のシナリオで結果が使用される場合に役立ちます。 これらは、変更の追跡情報を設定する必要がないため、より高速に実行できます。 データベースから取得されたエンティティを更新する必要がない場合は、追跡なしのクエリを使用することをお勧めします。 個別のクエリをスワップして、追跡なしにできます。 追跡なしのクエリでは、ローカルの変更や追加されたエンティティとは関係なく、データベースの内容に基づいた結果も取得されます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

また、コンテキスト インスタンスのレベルで、既定の追跡動作を変更できます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>識別子の解決

追跡クエリでは変更トラッカーが使用されます。そのため、EF Core では追跡クエリで ID 解決が実行されます。 エンティティを具体化するとき、それが既に追跡されている場合は、EF Core によって変更トラッカーから同じエンティティ インスタンスが返されます。 結果に同じエンティティが複数回含まれている場合は、そのたびに同じインスタンスが返されます。 追跡なしのクエリでは変更トラッカーが使用されず、ID 解決は実行されません。 そのため、同じエンティティが結果に複数回含まれている場合でも、エンティティの新しいインスタンスが返されます。 この動作は、EF Core 3.0 以前のバージョンでは異なります (「[以前のバージョン](#previous-versions)」を参照)。

EF Core 5.0 以降では、同じクエリで上記の両方の動作を組み合わせることができます。 つまり、追跡なしのクエリを使用できます。これにより、結果で ID 解決が行われます。 クエリ可能な `AsNoTracking()` 演算子と同じように、別の `AsNoTrackingWithIdentityResolution()` 演算子が追加されました。 <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior> 列挙型にも、関連するエントリが追加されています。 追跡なしで ID 解決を使用するようにクエリを構成すると、クエリ結果の生成時に、スタンドアロンの変更トラッカーがバックグラウンドで使用されます。そのため、各インスタンスが 1 回だけ具体化されます。 この変更トラッカーはコンテキスト内のものとは異なるため、結果はコンテキストによって追跡されません。 クエリが完全に列挙されると、変更トラッカーはスコープ外になり、必要に応じてガベージ コレクションが実行されます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

## <a name="tracking-and-custom-projections"></a>追跡とカスタム プロジェクション

クエリの結果の型がエンティティ型ではない場合でも、EF Core では既定で、結果に含まれているエンティティ型が引き続き追跡されます。 次のクエリでは、匿名の型が返され、結果セット内で `Blog` のインスタンスが追跡されます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

結果セットに LINQ の合成に由来するエンティティ型が含まれていた場合は、EF Core によってそれらが追跡されます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

結果セットにいかなるエンティティ型も含まれていなかった場合は、追跡なしが実行されます。 次のクエリでは、エンティティの値の一部を含む (ただし実際のエンティティ型のインスタンスは含まない) 匿名型が返されます。 クエリから生じた追跡対象のエンティティはありません。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core では、最上位レベルのプロジェクションでクライアント評価を行うことができます。 クライアント評価のために、EF Core によりエンティティ インスタンスが具体化された場合、それは追跡されます。 ここでは、`blog` エンティティがクライアント メソッド `StandardizeURL` に渡されているため、EF Core によって blog インスタンスも追跡されます。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core では、結果に含まれているキーなしのエンティティ インスタンスは追跡されません。 ただし、キーを持つその他のすべてのエンティティ型のインスタンスは、上記のルールに従って、EF Core によって追跡されます。

上記のルールの一部は、EF Core 3.0 以前では異なった動作をします。 詳細については、「[以前のバージョン](#previous-versions)」をご覧ください。

## <a name="previous-versions"></a>以前のバージョン

バージョン 3.0 より前の EF Core では、追跡の実行方法にいくつかの違いがありました。 注意すべき違いは次のとおりです。

- 「[クライアントとサーバーの評価](xref:core/querying/client-eval)」ページで説明されているように、バージョン 3.0 より前の EF Core では、クエリのあらゆる部分でのクライアント評価がサポートされていました。 クライアント評価によってエンティティの具体化が発生し、それは結果に含まれていませんでした。 そのため、EF Core では、追跡対象を検出するために、その結果が分析されていました。この設計には、次のようないくつかの相違点があります。
  - プロジェクションでのクライアント評価は、具体化を発生させましたが、具体化されたエンティティ インスタンスは返されず、追跡されませんでした。 次の例では、`blog` エンティティが追跡されませんでした。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - 場合によっては、EF Core では LINQ の合成に由来するオブジェクトが追跡されませんでした。 次の例では、`Post` が追跡されませんでした。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- クエリ結果にキーなしエンティティ型が含まれていた場合は、常にクエリ全体が追跡なしになりました。 つまり、結果に含まれている、キーを持つエンティティ型も追跡されませんでした。
- 以前は、EF Core により、追跡なしのクエリで ID 解決が実行されていました。 既に返されているエンティティを追跡するために、弱参照が使用されました。 そのため、結果セットに同じエンティティが複数回含まれていた場合は、そのたびに同じインスタンスが返されました。 同じ ID を持つ前の結果がスコープから外れ、ガベージ コレクションが行われても、EF Core により新しいインスタンスが返されました。
