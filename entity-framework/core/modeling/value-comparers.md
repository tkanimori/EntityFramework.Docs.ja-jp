---
title: 値の比較演算子-EF Core
description: 値比較子を使用してプロパティ値を比較 EF Core 方法を制御する
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: d07aee866a542f55c4e1074c5782e67cb4035a89
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616678"
---
# <a name="value-comparers"></a>値の比較演算子

> [!NOTE]  
> この機能は EF Core 3.0 で新たに追加されています。

> [!TIP]  
> このドキュメントのコードは、実行可能な [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)として GitHub にあります。

## <a name="background"></a>背景

EF Core では、次の場合にプロパティ値を比較する必要があります。

* [更新プログラムの変更の検出](xref:core/saving/basic)の一部としてプロパティが変更されているかどうかを確認する
* リレーションシップの解決時に2つのキー値が等しいかどうかを判断する

これは、int、bool、DateTime などの一般的なプリミティブ型に対して自動的に処理されます。

より複雑な型については、比較の実行方法を選択する必要があります。
たとえば、バイト配列は次のように比較できます。

* 新しいバイト配列が使用されている場合にのみ、違いが検出されるように、参照渡し
* 詳細な比較により、配列内のバイトの変化が検出されます。

既定では、EF Core は、キー以外のバイト配列に対してこれらの方法の1つ目を使用します。
つまり、参照のみが比較され、既存のバイト配列が新しいバイト配列に置き換えられた場合にのみ、変更が検出されます。
これは、SaveChanges を実行するときに多くの大きなバイト配列の深い比較を回避する、実際的な決定です。
ただし、別のイメージを使用したイメージがパフォーマンスの高い方法で処理されるという一般的なシナリオがあります。

一方、バイナリキーを表すためにバイト配列が使用されている場合、参照の等価性は機能しません。
FK プロパティが、比較する必要のある PK プロパティと _同じインスタンス_ に設定されていることはほとんどありません。
そのため、EF Core は、キーとして機能するバイト配列の詳細な比較を使用します。
これは、通常、バイナリキーが短いため、パフォーマンスが大幅に低下する可能性があります。

### <a name="snapshots"></a>スナップショット

変更可能な型の詳細な比較では、プロパティ値の詳細な "スナップショット" を作成する機能が EF Core に必要です。
代わりに参照をコピーするだけで、現在の値とスナップショットの両方が変更されます。これは、 _同じオブジェクト_であるためです。
したがって、変更可能な型に対して詳細比較を使用する場合は、ディープスナップショットも必要です。

## <a name="properties-with-value-converters"></a>値コンバーターを持つプロパティ

上の例では、EF Core によってバイト配列のネイティブマッピングがサポートされているため、適切な既定値を自動的に選択できます。
ただし、プロパティが [値コンバーター](xref:core/modeling/value-conversions)によってマップされている場合、EF Core は、必ず適切な比較を決定できません。
代わりに、EF Core は常に、プロパティの型によって定義された既定の等値比較を使用します。
多くの場合、これは適切ですが、より複雑な型をマップするときにオーバーライドする必要があります。

### <a name="simple-immutable-classes"></a>単純な変更できないクラス

値コンバーターを使用して単純な変更できないクラスをマップするプロパティについて考えてみます。

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

この型のプロパティでは、次の理由により、特別な比較やスナップショットは必要ありません。

* 異なるインスタンスが正しく比較されるように、等値がオーバーライドされます。
* 型は不変であるため、スナップショットの値を変更する可能性はありません。

この場合、EF Core の既定の動作はそのままです。

### <a name="simple-immutable-structs"></a>単純な変更できない構造体

単純な構造体のマッピングも単純であり、特別な比較子やスナップショットは必要ありません。

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core には、構造体のプロパティのコンパイル済みのメンバーごとの比較を生成するためのサポートが組み込まれています。
これは、構造体が EF に対して等値オーバーライドを必要としないことを意味しますが、 [他の理由](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)でこれを行うこともできます。
また、特別なスナップショットは必要ありません。構造体は変更できず、常にメンバーごとにコピーされるためです。
(これは変更可能な構造体にも当てはまりますが、変更 [可能な構造体は一般に避ける必要があり](/dotnet/csharp/write-safe-efficient-code)ます)。

### <a name="mutable-classes"></a>変更可能なクラス

可能な限り、変更できない型 (クラスまたは構造体) を値コンバーターと共に使用することをお勧めします。
これは通常、より効率的であり、変更可能な型を使用する場合よりも明確なセマンティクスを持ちます。

ただし、このような場合は、アプリケーションが変更できない型のプロパティを使用するのが一般的です。
たとえば、次のように、数値のリストを含むプロパティをマッピングします。

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

[ `List<T>` クラス](/dotnet/api/system.collections.generic.list-1):

* 参照の等価性があります。同じ値を含む2つのリストは、異なるものとして扱われます。
* 変更可能です。リスト内の値は、追加および削除できます。

リストプロパティの一般的な値の変換では、リストを JSON との間で変換することができます。

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

そのためには、プロパティにを設定して、 `ValueComparer<T>` この変換で正しい比較を使用 EF Core ようにする必要があります。

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> 値の比較子を設定するためのモデルビルダー ("fluent") API は、まだ実装されていません。
> 代わりに、上記のコードは、ビルダーによって公開されている下位レベルの IMutableProperty に対して ' Metadata ' として SetValueComparer を呼び出します。

コンストラクターは、 `ValueComparer<T>` 次の3つの式を受け取ります。

* 等しいかどうかをチェックする式
* ハッシュコードを生成するための式
* 値をスナップショットにする式  

この場合、比較は、数値のシーケンスが同じであるかどうかをチェックすることによって行われます。

同様に、ハッシュコードは同じ順序で作成されます。
(これは変更可能な値を超えるハッシュコードであるため、 [問題が発生](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)する可能性があります。
可能であれば、変更不可にしてください。)

スナップショットは、リストを ToList で複製することによって作成されます。
ここでも、リストを変換する場合にのみ必要です。
可能であれば、変更できないようにします。

> [!NOTE]  
> 値コンバーターと比較子は、単純なデリゲートではなく、式を使用して構築されます。
> これは、EF によって、これらの式がより複雑な式ツリーに挿入され、その後、entity shaper デリゲートにコンパイルされるためです。
> 概念的には、これはコンパイラのインライン展開に似ています。
> たとえば、単純な変換は、別のメソッドを呼び出して変換を行うのではなく、キャストでコンパイルされるだけである場合があります。

### <a name="key-comparers"></a>キー比較子

[背景] セクションでは、キーの比較によって特殊なセマンティクスが必要になる理由を説明します。
プライマリ、プリンシパル、または外部キーのプロパティでキーを設定するときに、キーに適した比較子を作成してください。

同じプロパティに異なるセマンティクスが必要なまれなケースでは、 [Setkeyvaluecomparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) を使用します。

> [!NOTE]  
> SetStructuralComparer は EF Core 5.0 で廃止されました。
> 代わりに、SetKeyValueComparer を使用してください。

### <a name="overriding-defaults"></a>既定値のオーバーライド

EF Core によって使用される既定の比較が適切でない場合があります。
たとえば、バイト配列の変異は、既定では EF Core で検出されません。
これは、プロパティで別の比較子を設定することによってオーバーライドできます。

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core では、バイトシーケンスを比較するため、バイト配列変更が検出されます。
