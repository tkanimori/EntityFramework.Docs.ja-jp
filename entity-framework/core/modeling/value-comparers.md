---
title: 値の比較演算子-EF Core
description: 値比較子を使用してプロパティ値を比較 EF Core 方法を制御する
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-comparers
ms.openlocfilehash: 5c5e5beee72230a331a8e1c88a2020dc5ad88ecf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983483"
---
# <a name="value-comparers"></a><span data-ttu-id="e4d27-103">値の比較演算子</span><span class="sxs-lookup"><span data-stu-id="e4d27-103">Value Comparers</span></span>

> [!NOTE]
> <span data-ttu-id="e4d27-104">この機能は EF Core 3.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e4d27-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]
> <span data-ttu-id="e4d27-105">このドキュメントのコードは、実行可能な [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/)として GitHub にあります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="e4d27-106">バックグラウンド</span><span class="sxs-lookup"><span data-stu-id="e4d27-106">Background</span></span>

<span data-ttu-id="e4d27-107">[変更の追跡](xref:core/change-tracking/index) とは、読み込まれたエンティティインスタンスでアプリケーションによって行われた変更を EF Core が自動的に判断することを意味します。これにより、が呼び出されたときに、これらの変更をデータベースに保存することができ <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-107">[Change tracking](xref:core/change-tracking/index) means that EF Core automatically determines what changes were performed by the application on a loaded entity instance, so that those changes can be saved back to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="e4d27-108">EF Core は、通常、インスタンスがデータベースから読み込まれたときにその *スナップショットを取得* し、そのスナップショットをアプリケーションに渡されたインスタンスと *比較* することによってこれを実行します。</span><span class="sxs-lookup"><span data-stu-id="e4d27-108">EF Core usually performs this by taking a *snapshot* of the instance when it's loaded from the database, and *comparing* that snapshot to the instance handed out to the application.</span></span>

<span data-ttu-id="e4d27-109">EF Core には、データベースで使用されるほとんどの標準的な種類を比較するための組み込みロジックが付属しているため、ユーザーは通常このトピックについて心配する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-109">EF Core comes with built-in logic for snapshotting and comparing most standard types used in databases, so users don't usually need to worry about this topic.</span></span> <span data-ttu-id="e4d27-110">ただし、プロパティが [値コンバーター](xref:core/modeling/value-conversions)によってマップされている場合、EF Core は、複雑になる可能性がある任意のユーザー型に対して比較を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-110">However, when a property is mapped through a [value converter](xref:core/modeling/value-conversions), EF Core needs to perform comparison on arbitrary user types, which may be complex.</span></span> <span data-ttu-id="e4d27-111">既定では、EF Core は型によって定義された既定の等値比較 (メソッドなど) を使用し `Equals` ます。スナップショットの場合は、スナップショットを作成するために [値型](/dotnet/csharp/language-reference/builtin-types/value-types) がコピーされます。 [参照型](/dotnet/csharp/language-reference/keywords/reference-types) の場合はコピーが行われず、同じインスタンスがスナップショットとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-111">By default, EF Core uses the default equality comparison defined by types (e.g. the `Equals` method); for snapshotting, [value types](/dotnet/csharp/language-reference/builtin-types/value-types) are copied to produce the snapshot, while for [reference types](/dotnet/csharp/language-reference/keywords/reference-types) no copying occurs, and the same instance is used as the snapshot.</span></span>

<span data-ttu-id="e4d27-112">組み込みの比較動作が適切でない場合は、ユーザーが *値の比較子* を提供することがあります。これには、スナップショットのロジック、ハッシュコードの比較と計算が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-112">In cases where the built-in comparison behavior isn't appropriate, users may provide a *value comparer*, which contains logic for snapshotting, comparing and calculating a hash code.</span></span> <span data-ttu-id="e4d27-113">たとえば、次の例では、プロパティの値変換を `List<int>` データベースの JSON 文字列に変換された値に設定し、適切な値の比較子も定義しています。</span><span class="sxs-lookup"><span data-stu-id="e4d27-113">For example, the following sets up value conversion for `List<int>` property to be value converted to a JSON string in the database, and defines an appropriate value comparer as well:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="e4d27-114">詳細については、以下の「変更可能な [クラス](#mutable-classes) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e4d27-114">See [mutable classes](#mutable-classes) below for further details.</span></span>

<span data-ttu-id="e4d27-115">リレーションシップを解決するときに2つのキー値が等しいかどうかを判断するときにも、値の比較演算子が使用されます。これについては以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="e4d27-115">Note that value comparers are also used when determining whether two key values are the same when resolving relationships; this is explained below.</span></span>

## <a name="shallow-vs-deep-comparison"></a><span data-ttu-id="e4d27-116">浅い比較と詳細な比較</span><span class="sxs-lookup"><span data-stu-id="e4d27-116">Shallow vs. deep comparison</span></span>

<span data-ttu-id="e4d27-117">小規模な場合は、 `int` EF Core の既定のロジックが適切に機能します。スナップショット時に値がそのままコピーされ、型の組み込みの等価比較と比較されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-117">For small, immutable value types such as `int`, EF Core's default logic works well: the value is copied as-is when snapshotted, and compared with the type's built-in equality comparison.</span></span> <span data-ttu-id="e4d27-118">独自の値の比較子を実装する場合は、ディープまたは浅い比較 (およびスナップショット) のロジックが適切かどうかを検討することが重要です。</span><span class="sxs-lookup"><span data-stu-id="e4d27-118">When implementing your own value comparer, it's important to consider whether deep or shallow comparison (and snapshotting) logic is appropriate.</span></span>

<span data-ttu-id="e4d27-119">バイト配列を検討します。これは任意の大きさにすることができます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-119">Consider byte arrays, which can be arbitrarily large.</span></span> <span data-ttu-id="e4d27-120">次の比較を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-120">These could be compared:</span></span>

* <span data-ttu-id="e4d27-121">新しいバイト配列が使用されている場合にのみ、違いが検出されるように、参照渡し</span><span class="sxs-lookup"><span data-stu-id="e4d27-121">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="e4d27-122">詳細な比較により、配列内のバイトの変化が検出されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-122">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="e4d27-123">既定では、EF Core は、キー以外のバイト配列に対してこれらの方法の1つ目を使用します。</span><span class="sxs-lookup"><span data-stu-id="e4d27-123">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span> <span data-ttu-id="e4d27-124">つまり、参照のみが比較され、既存のバイト配列が新しいバイト配列に置き換えられた場合にのみ、変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-124">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span> <span data-ttu-id="e4d27-125">これは、配列全体のコピーと、実行時のバイト対バイトの比較を回避する、実際的な決定です <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e4d27-125">This is a pragmatic decision that avoids copying entire arrays and comparing them byte-to-byte when executing <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="e4d27-126">つまり、ある画像を別のイメージと置き換えるという一般的なシナリオは、パフォーマンスの高い方法で処理されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-126">It means that the common scenario of replacing, say, one image with another is handled in a performant way.</span></span>

<span data-ttu-id="e4d27-127">一方、バイト配列がバイナリキーを表すために使用される場合、参照の等価性は機能しません。これは、FK プロパティが比較する必要がある PK プロパティと _同じインスタンス_ に設定されている可能性が非常に高いためです。</span><span class="sxs-lookup"><span data-stu-id="e4d27-127">On the other hand, reference equality would not work when byte arrays are used to represent binary keys, since it's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span> <span data-ttu-id="e4d27-128">そのため、EF Core はキーとして機能するバイト配列に対して詳細な比較を使用します。これは、通常、バイナリキーが短いため、パフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-128">Therefore, EF Core uses deep comparisons for byte arrays acting as keys; this is unlikely to have a big performance hit since binary keys are usually short.</span></span>

<span data-ttu-id="e4d27-129">選択した比較とスナップショットのロジックは互いに対応している必要があることに注意してください。詳細な比較を行うには、ディープスナップショットが正しく機能する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-129">Note that the chosen comparison and snapshotting logic must correspond to each other: deep comparison requires deep snapshotting to function correctly.</span></span>

## <a name="simple-immutable-classes"></a><span data-ttu-id="e4d27-130">単純な変更できないクラス</span><span class="sxs-lookup"><span data-stu-id="e4d27-130">Simple immutable classes</span></span>

<span data-ttu-id="e4d27-131">値コンバーターを使用して単純な変更できないクラスをマップするプロパティについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-131">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="e4d27-132">この型のプロパティでは、次の理由により、特別な比較やスナップショットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-132">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="e4d27-133">異なるインスタンスが正しく比較されるように、等値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-133">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="e4d27-134">型は不変であるため、スナップショットの値を変更する可能性はありません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-134">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="e4d27-135">この場合、EF Core の既定の動作はそのままです。</span><span class="sxs-lookup"><span data-stu-id="e4d27-135">So in this case the default behavior of EF Core is fine as it is.</span></span>

## <a name="simple-immutable-structs"></a><span data-ttu-id="e4d27-136">単純な変更できない構造体</span><span class="sxs-lookup"><span data-stu-id="e4d27-136">Simple immutable structs</span></span>

<span data-ttu-id="e4d27-137">単純な構造体のマッピングも単純であり、特別な比較子やスナップショットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-137">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="e4d27-138">EF Core には、構造体のプロパティのコンパイル済みのメンバーごとの比較を生成するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="e4d27-138">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span> <span data-ttu-id="e4d27-139">これは、構造体が EF Core に対して等値オーバーライドを持つ必要がないことを意味しますが、 [その他の理由](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)でこれを行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-139">This means structs don't need to have equality overridden for EF Core, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span> <span data-ttu-id="e4d27-140">また、構造体は不変であり、常にメンバーごとにコピーされるため、特別なスナップショットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-140">Also, special snapshotting is not needed since structs are immutable and are always copied memberwise anyway.</span></span> <span data-ttu-id="e4d27-141">(これは変更可能な構造体にも当てはまりますが、変更 [可能な構造体は一般に避ける必要があり](/dotnet/csharp/write-safe-efficient-code)ます)。</span><span class="sxs-lookup"><span data-stu-id="e4d27-141">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

## <a name="mutable-classes"></a><span data-ttu-id="e4d27-142">変更可能なクラス</span><span class="sxs-lookup"><span data-stu-id="e4d27-142">Mutable classes</span></span>

<span data-ttu-id="e4d27-143">可能な限り、変更できない型 (クラスまたは構造体) を値コンバーターと共に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e4d27-143">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span> <span data-ttu-id="e4d27-144">これは通常、より効率的であり、変更可能な型を使用する場合よりも明確なセマンティクスを持ちます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-144">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span> <span data-ttu-id="e4d27-145">ただし、このような場合は、アプリケーションが変更できない型のプロパティを使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="e4d27-145">However, that being said, it is common to use properties of types that the application cannot change.</span></span> <span data-ttu-id="e4d27-146">たとえば、次のように、数値のリストを含むプロパティをマッピングします。</span><span class="sxs-lookup"><span data-stu-id="e4d27-146">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="e4d27-147"><xref:System.Collections.Generic.List%601> クラスでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-147">The <xref:System.Collections.Generic.List%601> class:</span></span>

* <span data-ttu-id="e4d27-148">参照の等価性があります。同じ値を含む2つのリストは、異なるものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-148">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="e4d27-149">変更可能です。リスト内の値は、追加および削除できます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-149">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="e4d27-150">リストプロパティの一般的な値の変換では、リストを JSON との間で変換することができます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-150">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

### <a name="ef-core-50"></a>[<span data-ttu-id="e4d27-151">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="e4d27-151">EF Core 5.0</span></span>](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[<span data-ttu-id="e4d27-152">以前のバージョン</span><span class="sxs-lookup"><span data-stu-id="e4d27-152">Older versions</span></span>](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

<span data-ttu-id="e4d27-153">コンストラクターは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> 次の3つの式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-153">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor accepts three expressions:</span></span>

* <span data-ttu-id="e4d27-154">等しいかどうかをチェックする式</span><span class="sxs-lookup"><span data-stu-id="e4d27-154">An expression for checking equality</span></span>
* <span data-ttu-id="e4d27-155">ハッシュコードを生成するための式</span><span class="sxs-lookup"><span data-stu-id="e4d27-155">An expression for generating a hash code</span></span>
* <span data-ttu-id="e4d27-156">値をスナップショットにする式</span><span class="sxs-lookup"><span data-stu-id="e4d27-156">An expression to snapshot a value</span></span>

<span data-ttu-id="e4d27-157">この場合、比較は、数値のシーケンスが同じであるかどうかをチェックすることによって行われます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-157">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="e4d27-158">同様に、ハッシュコードは同じ順序で作成されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-158">Likewise, the hash code is built from this same sequence.</span></span> <span data-ttu-id="e4d27-159">(これは変更可能な値を超えるハッシュコードであるため、 [問題が発生](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-159">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span> <span data-ttu-id="e4d27-160">可能であれば、変更不可にしてください。)</span><span class="sxs-lookup"><span data-stu-id="e4d27-160">Be immutable instead if you can.)</span></span>

<span data-ttu-id="e4d27-161">スナップショットは、でリストを複製することによって作成され `ToList` ます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-161">The snapshot is created by cloning the list with `ToList`.</span></span> <span data-ttu-id="e4d27-162">ここでも、リストを変換する場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="e4d27-162">Again, this is only needed if the lists are going to be mutated.</span></span> <span data-ttu-id="e4d27-163">可能であれば、変更できないようにします。</span><span class="sxs-lookup"><span data-stu-id="e4d27-163">Be immutable instead if you can.</span></span>

> [!NOTE]
> <span data-ttu-id="e4d27-164">値コンバーターと比較子は、単純なデリゲートではなく、式を使用して構築されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-164">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span> <span data-ttu-id="e4d27-165">これは、EF Core によって、これらの式がより複雑な式ツリーに挿入され、その後、エンティティ shaper デリゲートにコンパイルされるためです。</span><span class="sxs-lookup"><span data-stu-id="e4d27-165">This is because EF Core inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span> <span data-ttu-id="e4d27-166">概念的には、これはコンパイラのインライン展開に似ています。</span><span class="sxs-lookup"><span data-stu-id="e4d27-166">Conceptually, this is similar to compiler inlining.</span></span> <span data-ttu-id="e4d27-167">たとえば、単純な変換は、別のメソッドを呼び出して変換を行うのではなく、キャストでコンパイルされるだけである場合があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-167">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

## <a name="key-comparers"></a><span data-ttu-id="e4d27-168">キー比較子</span><span class="sxs-lookup"><span data-stu-id="e4d27-168">Key comparers</span></span>

<span data-ttu-id="e4d27-169">[背景] セクションでは、キーの比較によって特殊なセマンティクスが必要になる理由を説明します。</span><span class="sxs-lookup"><span data-stu-id="e4d27-169">The background section covers why key comparisons may require special semantics.</span></span> <span data-ttu-id="e4d27-170">プライマリ、プリンシパル、または外部キーのプロパティでキーを設定するときに、キーに適した比較子を作成してください。</span><span class="sxs-lookup"><span data-stu-id="e4d27-170">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="e4d27-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>同じプロパティに異なるセマンティクスが必要なまれなケースでは、を使用します。</span><span class="sxs-lookup"><span data-stu-id="e4d27-171">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]
> <span data-ttu-id="e4d27-172"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> は EF Core 5.0 で廃止されました。</span><span class="sxs-lookup"><span data-stu-id="e4d27-172"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> has been obsoleted in EF Core 5.0.</span></span> <span data-ttu-id="e4d27-173">代わりに <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> を使用してください</span><span class="sxs-lookup"><span data-stu-id="e4d27-173">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> instead.</span></span>

## <a name="overriding-the-default-comparer"></a><span data-ttu-id="e4d27-174">既定の比較子のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="e4d27-174">Overriding the default comparer</span></span>

<span data-ttu-id="e4d27-175">EF Core によって使用される既定の比較が適切でない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e4d27-175">Sometimes the default comparison used by EF Core may not be appropriate.</span></span> <span data-ttu-id="e4d27-176">たとえば、バイト配列の変異は、既定では EF Core で検出されません。</span><span class="sxs-lookup"><span data-stu-id="e4d27-176">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span> <span data-ttu-id="e4d27-177">これは、プロパティで別の比較子を設定することによってオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-177">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="e4d27-178">EF Core では、バイトシーケンスを比較するため、バイト配列変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="e4d27-178">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
