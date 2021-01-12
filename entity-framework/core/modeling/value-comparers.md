---
title: 値の比較演算子-EF Core
description: 値比較子を使用してプロパティ値を比較 EF Core 方法を制御する
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 618341315de05f6efae8f43384809ed72226e18b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128512"
---
# <a name="value-comparers"></a>値の比較演算子

> [!NOTE]
> この機能は EF Core 3.0 で導入されました。

> [!TIP]
> このドキュメントのコードは、実行可能な [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)として GitHub にあります。

## <a name="background"></a>バックグラウンド

変更の追跡とは、読み込まれたエンティティインスタンスでアプリケーションによって行われた変更を EF Core が自動的に判断することを意味します。これにより、が呼び出されたときに、これらの変更をデータベースに保存することができ <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。 EF Core は、通常、インスタンスがデータベースから読み込まれたときにその *スナップショットを取得* し、そのスナップショットをアプリケーションに渡されたインスタンスと *比較* することによってこれを実行します。

EF Core には、データベースで使用されるほとんどの標準的な種類を比較するための組み込みロジックが付属しているため、ユーザーは通常このトピックについて心配する必要はありません。 ただし、プロパティが [値コンバーター](xref:core/modeling/value-conversions)によってマップされている場合、EF Core は、複雑になる可能性がある任意のユーザー型に対して比較を実行する必要があります。 既定では、EF Core は型によって定義された既定の等値比較 (メソッドなど) を使用し `Equals` ます。スナップショットの場合は、スナップショットを作成するために値型がコピーされます。参照型の場合はコピーが行われず、同じインスタンスがスナップショットとして使用されます。

組み込みの比較動作が適切でない場合は、ユーザーが *値の比較子* を提供することがあります。これには、スナップショットのロジック、ハッシュコードの比較と計算が含まれます。 たとえば、次の例では、プロパティの値変換を `List<int>` データベースの JSON 文字列に変換された値に設定し、適切な値の比較子も定義しています。

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

詳細については、以下の「変更可能な [クラス](#mutable-classes) 」を参照してください。

リレーションシップを解決するときに2つのキー値が等しいかどうかを判断するときにも、値の比較演算子が使用されます。これについては以下で説明します。

## <a name="shallow-vs-deep-comparison"></a>浅い比較と詳細な比較

小規模な場合は、 `int` EF Core の既定のロジックが適切に機能します。スナップショット時に値がそのままコピーされ、型の組み込みの等価比較と比較されます。 独自の値の比較子を実装する場合は、ディープまたは浅い比較 (およびスナップショット) のロジックが適切かどうかを検討することが重要です。

バイト配列を検討します。これは任意の大きさにすることができます。 次の比較を行うことができます。

* 新しいバイト配列が使用されている場合にのみ、違いが検出されるように、参照渡し
* 詳細な比較により、配列内のバイトの変化が検出されます。

既定では、EF Core は、キー以外のバイト配列に対してこれらの方法の1つ目を使用します。 つまり、参照のみが比較され、既存のバイト配列が新しいバイト配列に置き換えられた場合にのみ、変更が検出されます。 これは、配列全体のコピーを回避し、実行時にバイト対バイトを比較することを回避するための実用的な決定です <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。また、ある画像を別のイメージと交換する一般的なシナリオは、パフォーマンスの高い方法で処理されます。

一方、バイト配列がバイナリキーを表すために使用される場合、参照の等価性は機能しません。これは、FK プロパティが比較する必要がある PK プロパティと _同じインスタンス_ に設定されている可能性が非常に高いためです。 そのため、EF Core はキーとして機能するバイト配列に対して詳細な比較を使用します。これは、通常、バイナリキーが短いため、パフォーマンスが大幅に低下する可能性があります。

選択した比較とスナップショットのロジックは互いに対応している必要があることに注意してください。詳細な比較を行うには、ディープスナップショットが正しく機能する必要があります。

## <a name="simple-immutable-classes"></a>単純な変更できないクラス

値コンバーターを使用して単純な変更できないクラスをマップするプロパティについて考えてみます。

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

この型のプロパティでは、次の理由により、特別な比較やスナップショットは必要ありません。

* 異なるインスタンスが正しく比較されるように、等値がオーバーライドされます。
* 型は不変であるため、スナップショットの値を変更する可能性はありません。

この場合、EF Core の既定の動作はそのままです。

## <a name="simple-immutable-structs"></a>単純な変更できない構造体

単純な構造体のマッピングも単純であり、特別な比較子やスナップショットは必要ありません。

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core には、構造体のプロパティのコンパイル済みのメンバーごとの比較を生成するためのサポートが組み込まれています。
これは、構造体が EF Core に対して等値オーバーライドを持つ必要がないことを意味しますが、 [その他の理由](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)でこれを行うこともできます。
また、構造体は不変であり、常にメンバーごとにコピーされるため、特別なスナップショットは必要ありません。
(これは変更可能な構造体にも当てはまりますが、変更 [可能な構造体は一般に避ける必要があり](/dotnet/csharp/write-safe-efficient-code)ます)。

## <a name="mutable-classes"></a>変更可能なクラス

可能な限り、変更できない型 (クラスまたは構造体) を値コンバーターと共に使用することをお勧めします。
これは通常、より効率的であり、変更可能な型を使用する場合よりも明確なセマンティクスを持ちます。

ただし、このような場合は、アプリケーションが変更できない型のプロパティを使用するのが一般的です。
たとえば、次のように、数値のリストを含むプロパティをマッピングします。

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<xref:System.Collections.Generic.List%601> クラスでは次のことが行われます。

* 参照の等価性があります。同じ値を含む2つのリストは、異なるものとして扱われます。
* 変更可能です。リスト内の値は、追加および削除できます。

リストプロパティの一般的な値の変換では、リストを JSON との間で変換することができます。

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[以前のバージョン](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

コンストラクターは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 次の3つの式を受け取ります。

* 等しいかどうかをチェックする式
* ハッシュコードを生成するための式
* 値をスナップショットにする式

この場合、比較は、数値のシーケンスが同じであるかどうかをチェックすることによって行われます。

同様に、ハッシュコードは同じ順序で作成されます。
(これは変更可能な値を超えるハッシュコードであるため、 [問題が発生](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)する可能性があります。
可能であれば、変更不可にしてください。)

スナップショットは、でリストを複製することによって作成され `ToList` ます。
ここでも、リストを変換する場合にのみ必要です。
可能であれば、変更できないようにします。

> [!NOTE]
> 値コンバーターと比較子は、単純なデリゲートではなく、式を使用して構築されます。
> これは、EF Core によって、これらの式がより複雑な式ツリーに挿入され、その後、エンティティ shaper デリゲートにコンパイルされるためです。
> 概念的には、これはコンパイラのインライン展開に似ています。
> たとえば、単純な変換は、別のメソッドを呼び出して変換を行うのではなく、キャストでコンパイルされるだけである場合があります。

## <a name="key-comparers"></a>キー比較子

[背景] セクションでは、キーの比較によって特殊なセマンティクスが必要になる理由を説明します。
プライマリ、プリンシパル、または外部キーのプロパティでキーを設定するときに、キーに適した比較子を作成してください。

<xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>同じプロパティに異なるセマンティクスが必要なまれなケースでは、を使用します。

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> は EF Core 5.0 で廃止されました。 代わりに <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> を使用してください

## <a name="overriding-the-default-comparer"></a>既定の比較子のオーバーライド

EF Core によって使用される既定の比較が適切でない場合があります。
たとえば、バイト配列の変異は、既定では EF Core で検出されません。
これは、プロパティで別の比較子を設定することによってオーバーライドできます。

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core では、バイトシーケンスを比較するため、バイト配列変更が検出されます。
