---
title: 値の比較演算子-EF Core
description: 値比較子を使用してプロパティ値を比較 EF Core 方法を制御する
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526785"
---
# <a name="value-comparers"></a><span data-ttu-id="4a365-103">値の比較演算子</span><span class="sxs-lookup"><span data-stu-id="4a365-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="4a365-104">この機能は EF Core 3.0 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="4a365-104">This feature is new in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="4a365-105">このドキュメントのコードは、実行可能な[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)として GitHub にあります。</span><span class="sxs-lookup"><span data-stu-id="4a365-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="4a365-106">バックグラウンド</span><span class="sxs-lookup"><span data-stu-id="4a365-106">Background</span></span>

<span data-ttu-id="4a365-107">EF Core では、次の場合にプロパティ値を比較する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="4a365-108">[更新プログラムの変更の検出](xref:core/saving/basic)の一部としてプロパティが変更されているかどうかを確認する</span><span class="sxs-lookup"><span data-stu-id="4a365-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="4a365-109">リレーションシップの解決時に2つのキー値が等しいかどうかを判断する</span><span class="sxs-lookup"><span data-stu-id="4a365-109">Determining whether two key values are the same when resolving relationships</span></span> 

<span data-ttu-id="4a365-110">これは、int、bool、DateTime などの一般的なプリミティブ型に対して自動的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="4a365-111">より複雑な型については、比較の実行方法を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="4a365-112">たとえば、バイト配列は次のように比較できます。</span><span class="sxs-lookup"><span data-stu-id="4a365-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="4a365-113">新しいバイト配列が使用されている場合にのみ、違いが検出されるように、参照渡し</span><span class="sxs-lookup"><span data-stu-id="4a365-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="4a365-114">詳細な比較により、配列内のバイトの変化が検出されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="4a365-115">既定では、EF Core は、キー以外のバイト配列に対してこれらの方法の1つ目を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a365-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="4a365-116">つまり、参照のみが比較され、既存のバイト配列が新しいバイト配列に置き換えられた場合にのみ、変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="4a365-117">これは、SaveChanges を実行するときに多くの大きなバイト配列の深い比較を回避する、実際的な決定です。</span><span class="sxs-lookup"><span data-stu-id="4a365-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="4a365-118">ただし、別のイメージを使用したイメージがパフォーマンスの高い方法で処理されるという一般的なシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="4a365-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="4a365-119">一方、バイナリキーを表すためにバイト配列が使用されている場合、参照の等価性は機能しません。</span><span class="sxs-lookup"><span data-stu-id="4a365-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="4a365-120">FK プロパティが、比較する必要のある PK プロパティと_同じインスタンス_に設定されていることはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="4a365-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="4a365-121">そのため、EF Core は、キーとして機能するバイト配列の詳細な比較を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a365-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="4a365-122">これは、通常、バイナリキーが短いため、パフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="4a365-123">スナップショット</span><span class="sxs-lookup"><span data-stu-id="4a365-123">Snapshots</span></span>

<span data-ttu-id="4a365-124">変更可能な型の詳細な比較では、プロパティ値の詳細な "スナップショット" を作成する機能が EF Core に必要です。</span><span class="sxs-lookup"><span data-stu-id="4a365-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="4a365-125">代わりに参照をコピーするだけで、現在の値とスナップショットの両方が変更されます。これは、_同じオブジェクト_であるためです。</span><span class="sxs-lookup"><span data-stu-id="4a365-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="4a365-126">したがって、変更可能な型に対して詳細比較を使用する場合は、ディープスナップショットも必要です。</span><span class="sxs-lookup"><span data-stu-id="4a365-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="4a365-127">値コンバーターを持つプロパティ</span><span class="sxs-lookup"><span data-stu-id="4a365-127">Properties with value converters</span></span>

<span data-ttu-id="4a365-128">上の例では、EF Core によってバイト配列のネイティブマッピングがサポートされているため、適切な既定値を自動的に選択できます。</span><span class="sxs-lookup"><span data-stu-id="4a365-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="4a365-129">ただし、プロパティが[値コンバーター](xref:core/modeling/value-conversions)によってマップされている場合、EF Core は、必ず適切な比較を決定できません。</span><span class="sxs-lookup"><span data-stu-id="4a365-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="4a365-130">代わりに、EF Core は常に、プロパティの型によって定義された既定の等値比較を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a365-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="4a365-131">多くの場合、これは適切ですが、より複雑な型をマップするときにオーバーライドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="4a365-132">単純な変更できないクラス</span><span class="sxs-lookup"><span data-stu-id="4a365-132">Simple immutable classes</span></span>

<span data-ttu-id="4a365-133">値コンバーターを使用して単純な変更できないクラスをマップするプロパティについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4a365-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="4a365-134">この型のプロパティでは、次の理由により、特別な比較やスナップショットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4a365-134">Properties of this type do not need special comparisons or snapshots because:</span></span>
* <span data-ttu-id="4a365-135">異なるインスタンスが正しく比較されるように、等値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4a365-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="4a365-136">型は不変であるため、スナップショットの値を変更する可能性はありません。</span><span class="sxs-lookup"><span data-stu-id="4a365-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="4a365-137">この場合、EF Core の既定の動作はそのままです。</span><span class="sxs-lookup"><span data-stu-id="4a365-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="4a365-138">単純な変更できない構造体</span><span class="sxs-lookup"><span data-stu-id="4a365-138">Simple immutable Structs</span></span>

<span data-ttu-id="4a365-139">単純な構造体のマッピングも単純であり、特別な比較子やスナップショットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4a365-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="4a365-140">EF Core には、構造体のプロパティのコンパイル済みのメンバーごとの比較を生成するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="4a365-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="4a365-141">これは、構造体が EF に対して等値オーバーライドを必要としないことを意味しますが、[他の理由](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)でこれを行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="4a365-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="4a365-142">また、特別なスナップショットは必要ありません。構造体は変更できず、常にメンバーごとにコピーされるためです。</span><span class="sxs-lookup"><span data-stu-id="4a365-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="4a365-143">(これは変更可能な構造体にも当てはまりますが、変更[可能な構造体は一般に避ける必要があり](/dotnet/csharp/write-safe-efficient-code)ます)。</span><span class="sxs-lookup"><span data-stu-id="4a365-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="4a365-144">変更可能なクラス</span><span class="sxs-lookup"><span data-stu-id="4a365-144">Mutable classes</span></span>

<span data-ttu-id="4a365-145">可能な限り、変更できない型 (クラスまたは構造体) を値コンバーターと共に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4a365-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="4a365-146">これは通常、より効率的であり、変更可能な型を使用する場合よりも明確なセマンティクスを持ちます。</span><span class="sxs-lookup"><span data-stu-id="4a365-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="4a365-147">ただし、このような場合は、アプリケーションが変更できない型のプロパティを使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="4a365-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="4a365-148">たとえば、次のように、数値のリストを含むプロパティをマッピングします。</span><span class="sxs-lookup"><span data-stu-id="4a365-148">For example, mapping a property containing a list of numbers:</span></span> 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="4a365-149">[ `List<T>` クラス](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span><span class="sxs-lookup"><span data-stu-id="4a365-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span></span>
* <span data-ttu-id="4a365-150">参照の等価性があります。同じ値を含む2つのリストは、異なるものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="4a365-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="4a365-151">変更可能です。リスト内の値は、追加および削除できます。</span><span class="sxs-lookup"><span data-stu-id="4a365-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="4a365-152">リストプロパティの一般的な値の変換では、リストを JSON との間で変換することができます。</span><span class="sxs-lookup"><span data-stu-id="4a365-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="4a365-153">そのためには、プロパティにを設定して、 `ValueComparer<T>` この変換で正しい比較を使用 EF Core ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="4a365-154">値の比較子を設定するためのモデルビルダー ("fluent") API は、まだ実装されていません。</span><span class="sxs-lookup"><span data-stu-id="4a365-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="4a365-155">代わりに、上記のコードは、ビルダーによって公開されている下位レベルの IMutableProperty に対して ' Metadata ' として SetValueComparer を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4a365-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="4a365-156">コンストラクターは、 `ValueComparer<T>` 次の3つの式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4a365-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>
* <span data-ttu-id="4a365-157">等しいかどうかをチェックする式</span><span class="sxs-lookup"><span data-stu-id="4a365-157">An expression for checking equality</span></span>
* <span data-ttu-id="4a365-158">ハッシュコードを生成するための式</span><span class="sxs-lookup"><span data-stu-id="4a365-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="4a365-159">値をスナップショットにする式</span><span class="sxs-lookup"><span data-stu-id="4a365-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="4a365-160">この場合、比較は、数値のシーケンスが同じであるかどうかをチェックすることによって行われます。</span><span class="sxs-lookup"><span data-stu-id="4a365-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="4a365-161">同様に、ハッシュコードは同じ順序で作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="4a365-162">(これは変更可能な値を超えるハッシュコードであるため、[問題が発生](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="4a365-163">可能であれば、変更不可にしてください。)</span><span class="sxs-lookup"><span data-stu-id="4a365-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="4a365-164">スナップショットは、リストを ToList で複製することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="4a365-165">ここでも、リストを変換する場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="4a365-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="4a365-166">可能であれば、変更できないようにします。</span><span class="sxs-lookup"><span data-stu-id="4a365-166">Be immutable instead if you can.</span></span> 

> [!NOTE]  
> <span data-ttu-id="4a365-167">値コンバーターと比較子は、単純なデリゲートではなく、式を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="4a365-168">これは、EF によって、これらの式がより複雑な式ツリーに挿入され、その後、entity shaper デリゲートにコンパイルされるためです。</span><span class="sxs-lookup"><span data-stu-id="4a365-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="4a365-169">概念的には、これはコンパイラのインライン展開に似ています。</span><span class="sxs-lookup"><span data-stu-id="4a365-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="4a365-170">たとえば、単純な変換は、別のメソッドを呼び出して変換を行うのではなく、キャストでコンパイルされるだけである場合があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>    

### <a name="key-comparers"></a><span data-ttu-id="4a365-171">キー比較子</span><span class="sxs-lookup"><span data-stu-id="4a365-171">Key comparers</span></span>

<span data-ttu-id="4a365-172">[背景] セクションでは、キーの比較によって特殊なセマンティクスが必要になる理由を説明します。</span><span class="sxs-lookup"><span data-stu-id="4a365-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="4a365-173">プライマリ、プリンシパル、または外部キーのプロパティでキーを設定するときに、キーに適した比較子を作成してください。</span><span class="sxs-lookup"><span data-stu-id="4a365-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="4a365-174">同じプロパティに異なるセマンティクスが必要なまれなケースでは、 [Setkeyvaluecomparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4a365-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="4a365-175">SetStructuralComparer は EF Core 5.0 で廃止されました。</span><span class="sxs-lookup"><span data-stu-id="4a365-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="4a365-176">代わりに、SetKeyValueComparer を使用してください。</span><span class="sxs-lookup"><span data-stu-id="4a365-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="4a365-177">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="4a365-177">Overriding defaults</span></span>

<span data-ttu-id="4a365-178">EF Core によって使用される既定の比較が適切でない場合があります。</span><span class="sxs-lookup"><span data-stu-id="4a365-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="4a365-179">たとえば、バイト配列の変異は、既定では EF Core で検出されません。</span><span class="sxs-lookup"><span data-stu-id="4a365-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="4a365-180">これは、プロパティで別の比較子を設定することによってオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="4a365-180">This can be overridden by setting a different comparer on the property:</span></span> 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="4a365-181">EF Core では、バイトシーケンスを比較するため、バイト配列変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="4a365-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
