---
title: Null 許容の参照型の使用-EF Core
description: Entity Framework Core を使用する場合の C# null 許容の参照型の操作
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 0747b1328458fbaddd9e3cca117e378bbad5b365
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543433"
---
# <a name="working-with-nullable-reference-types"></a>Null 許容の参照型の使用

C# 8 では、null 値を [許容する参照型 (NRT)](/dotnet/csharp/tutorials/nullable-reference-types)と呼ばれる新しい機能が導入され、参照型に注釈を付けて、null を含むかどうかを示すことができます。 この機能を初めて使用する場合は、C# のドキュメントを参照してください。

このページでは、null 許容の参照型に対する EF Core のサポートについて説明し、それらを操作するためのベストプラクティスについて説明します。

## <a name="required-and-optional-properties"></a>必須および省略可能なプロパティ

必須およびオプションのプロパティに関する主なドキュメントと、null 許容の参照型との相互作用については、 [必須プロパティと省略可能なプロパティ](xref:core/modeling/entity-properties#required-and-optional-properties) に関するページを参照してください。 まず最初にこのページを読むことをお勧めします。

> [!NOTE]
> 既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。 リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。

## <a name="non-nullable-properties-and-initialization"></a>Null 非許容のプロパティと初期化

Null 値を許容する参照型が有効になっている場合、C# コンパイラは、初期化されていない null 非許容のプロパティに対して警告を出力します。 その結果、次のように、エンティティ型を記述する一般的な方法は使用できません。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=5-6)]

[コンストラクターバインド](xref:core/modeling/constructors) は、null 非許容のプロパティが初期化されるようにするための便利な手法です。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

残念ながら、一部のシナリオでは、コンストラクターバインドをオプションにすることはできません。たとえば、ナビゲーションプロパティは、この方法で初期化することはできません。

必須のナビゲーションプロパティでは、特定のプリンシパルに依存関係が常に存在しますが、プログラムのその時点でのニーズに応じて、特定のクエリによって読み込まれないことがあります ([データを読み込むためのさまざまなパターンを参照してください](xref:core/querying/related-data))。 同時に、これらのプロパティを null 許容にすることは望ましくありません。これは、必要に応じて、これらのプロパティへのすべてのアクセスが null をチェックするようにするためです。

これらのシナリオを処理する方法の1つは、null 許容の [バッキングフィールド](xref:core/modeling/backing-field)を持つ null 非許容プロパティを持つことです。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

ナビゲーションプロパティは null 非許容であるため、必要なナビゲーションが構成されます。また、ナビゲーションが適切に読み込まれている限り、依存するにはプロパティを使用してアクセスできます。 ただし、最初に関連エンティティを適切に読み込むことなく、プロパティにアクセスした場合は、API コントラクトが正しく使用されていないため、InvalidOperationException がスローされます。 EF は、プロパティではなく、常にバッキングフィールドにアクセスするように構成する必要があることに注意してください。これは、設定が解除された場合でも値を読み取ることができるようにするためです。これを行う方法については、 [バックアップフィールド](xref:core/modeling/backing-field) に関するドキュメントを参照してください。を指定して `PropertyAccessMode.Field` 、構成が正しいことを確認することを検討してください。

Terser として、null に対応していない演算子 (!) のヘルプを使用して、単純にプロパティを null に初期化することができます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

実際の null 値は、プログラミングのバグの結果としては決して観察されません。たとえば、関連エンティティを事前に適切に読み込むことなくナビゲーションプロパティにアクセスする場合などです。

> [!NOTE]
> 複数の関連エンティティへの参照を含むコレクションナビゲーションは、常に null 非許容にする必要があります。 空のコレクションは、関連するエンティティが存在しないことを意味しますが、リスト自体を null にすることはできません。

## <a name="dbcontext-and-dbset"></a>DbContext と Dbcontext

コンテキスト型で初期化されていない DbSet プロパティを使用する一般的な方法も問題になります。コンパイラが警告を出力するためです。 これは次のように修正できます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

もう1つの方法として、null 非許容の自動プロパティを使用しますが、null に初期化するには、null を使用しない演算子 (!) を使用して、コンパイラの警告をサイレント状態にします。 DbContext 基本コンストラクターによって、すべての Dbcontext プロパティが初期化され、null が検出されることはありません。

## <a name="navigating-and-including-nullable-relationships"></a>Null 許容リレーションシップの移動と操作

オプションのリレーションシップを処理する場合、実際の null 参照例外が発生しないというコンパイラの警告が表示される可能性があります。 LINQ クエリの変換と実行時に、オプションの関連エンティティが存在しない場合は、スローされるのではなく、その関連エンティティへのナビゲーションが無視されることが EF Core 保証されます。 ただし、コンパイラはこの EF Core 保証を認識しないため、LINQ クエリがメモリ内で実行されたかのように、LINQ to Objects を使用して警告を生成します。 結果として、null を許容しない演算子 (!) を使用して、実際の null 値が不可能であることをコンパイラに通知する必要があります。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?name=Navigating)]

次のような場合にも同様の問題が発生します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?name=Including&highlight=2)]

これを頻繁に実行し、問題のエンティティ型が EF Core クエリで主に (または排他的に) 使用されている場合は、ナビゲーションプロパティを null 非許容にし、Fluent API またはデータ注釈を使用してオプションとして構成することを検討してください。 これにより、リレーションシップを省略可能にしたまま、すべてのコンパイラ警告が削除されます。ただし、EF Core の外部でエンティティを走査した場合、プロパティには null 非許容として注釈が付けられますが、null 値を確認することができます。

## <a name="limitations"></a>制限事項

* リバースエンジニアリングでは、現在、 [C# 8 の null 許容参照型 (NRTs)](/dotnet/csharp/tutorials/nullable-reference-types)はサポートされていません。 EF Core は、機能がオフであることを前提とする c# コードを常に生成します。 たとえば、null 値が許容されるテキスト列は、 `string` `string?` プロパティが必須かどうかを構成するために使用される Fluent API またはデータ注釈を使用してではなく、型のプロパティとしてスキャフォールディングされます。 スキャフォールディングコードを編集し、C# の Null 値の許容属性に置き換えることができます。 Null 許容型参照型のスキャフォールディングサポートは、 [#15520](https://github.com/dotnet/efcore/issues/15520)問題によって追跡されます。
* EF Core のパブリック API サーフェイスには、null 値の許容属性 (パブリック API が "無関係") に対してまだ注釈が付けられていないため、NRT 機能が有効になっているときに使用するのが困難な場合があります。 これには、特に、EF Core によって公開される非同期 LINQ 演算子 (たとえば、「」を含む) が含ま[れます。](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 6.0 リリースでは、このことに対処する予定です。
