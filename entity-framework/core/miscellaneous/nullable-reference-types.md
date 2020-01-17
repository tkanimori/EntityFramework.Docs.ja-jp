---
title: Null 許容の参照型の使用-EF Core
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: c16a475c363320cd18804a4efe78ccae1ae22f0d
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124354"
---
# <a name="working-with-nullable-reference-types"></a>Null 許容の参照型の使用

C#8では、null 値を[許容する参照型](/dotnet/csharp/tutorials/nullable-reference-types)と呼ばれる新しい機能が導入されました。これにより、参照型に注釈を付け、null を含むかどうかを示すことができます。 この機能を初めて使用する場合は、 C#ドキュメントを読むことをお勧めします。

このページでは、null 許容の参照型に対する EF Core のサポートについて説明し、それらを操作するためのベストプラクティスについて説明します。

## <a name="required-and-optional-properties"></a>必須および省略可能なプロパティ

必須およびオプションのプロパティに関する主なドキュメントと、null 許容の参照型との相互作用については、[必須プロパティと省略可能なプロパティ](xref:core/modeling/entity-properties#required-and-optional-properties)に関するページを参照してください。 まず最初にこのページを読むことをお勧めします。

> [!NOTE]
> 既存のプロジェクトで null 値を許容する参照型を有効にする場合は注意してください。以前にオプションとして構成されていた参照型プロパティは、明示的に null 値が指定されていない限り、必須として構成されます。 リレーショナルデータベーススキーマを管理する場合、これにより、データベース列の null 値の許容属性を変更する移行が生成される可能性があります。

## <a name="dbcontext-and-dbset"></a>DbContext と Dbcontext

Null 値を許容する参照型がC#有効になっている場合、初期化されていない null 以外のプロパティについては、コンパイラによって警告が出力されます。 その結果、コンテキストで null 非許容の `DbSet` を定義する一般的な方法では、警告が生成されるようになりました。 ただし、EF Core は常に DbContext 派生型のすべての `DbSet` プロパティを初期化するので、コンパイラがこれを認識しない場合でも、必ず null にならないことが保証されます。 したがって、`DbSet` のプロパティを null 非許容にすることをお勧めします。 null チェックを行わずにこれらのプロパティにアクセスできるようにします。また、null 非対応演算子 (!) を使用して明示的に null に設定することにより、コンパイラの警告をサイレント状態にすることもできます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

## <a name="non-nullable-properties-and-initialization"></a>Null 非許容のプロパティと初期化

初期化されていない null 非許容の参照型に対するコンパイラの警告は、エンティティ型の通常のプロパティにも問題があります。 上記の例では、コンストラクターバインディングを使用して、これらの警告を回避しました。[コンストラクターバインド](xref:core/modeling/constructors)は、null 非許容のプロパティで完全に動作する機能であり、常に初期化されていることを保証します。 ただし、シナリオによっては、コンストラクターバインドがオプションではない場合があります。たとえば、ナビゲーションプロパティをこの方法で初期化することはできません。

必須のナビゲーションプロパティでは、特定のプリンシパルに依存関係が常に存在しますが、プログラムのその時点でのニーズに応じて、特定のクエリによって読み込まれないことがあります ([データを読み込むためのさまざまなパターンを参照してください](xref:core/querying/related-data))。 同時に、これらのプロパティを null 許容にすることは望ましくありません。これは、必要に応じて、これらのプロパティへのすべてのアクセスが null をチェックするようにするためです。

これらのシナリオを処理する方法の1つは、null 許容の[バッキングフィールド](xref:core/modeling/backing-field)を持つ null 非許容プロパティを持つことです。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

ナビゲーションプロパティは null 非許容であるため、必要なナビゲーションが構成されます。また、ナビゲーションが適切に読み込まれている限り、依存するにはプロパティを使用してアクセスできます。 ただし、最初に関連エンティティを適切に読み込むことなく、プロパティにアクセスした場合は、API コントラクトが正しく使用されていないため、InvalidOperationException がスローされます。 EF は、プロパティではなく、常にバッキングフィールドにアクセスするように構成する必要があることに注意してください。これは、設定が解除された場合でも値を読み取ることができるようにするためです。この方法については、[バックアップフィールド](xref:core/modeling/backing-field)に関するドキュメントを参照してください。 `PropertyAccessMode.Field` を指定して、構成が正しいことを確認することを検討してください。

Terser として、null に対応していない演算子 (!) のヘルプを使用して、単純にプロパティを null に初期化することができます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

実際の null 値は、プログラミングのバグの結果としては決して観察されません。たとえば、関連エンティティを事前に適切に読み込むことなくナビゲーションプロパティにアクセスする場合などです。

> [!NOTE]
> 複数の関連エンティティへの参照を含むコレクションナビゲーションは、常に null 非許容にする必要があります。 空のコレクションは、関連するエンティティが存在しないことを意味しますが、リスト自体を null にすることはできません。

## <a name="navigating-and-including-nullable-relationships"></a>Null 許容リレーションシップの移動と操作

オプションのリレーションシップを処理する場合、実際の null 参照例外が発生しないというコンパイラの警告が表示される可能性があります。 LINQ クエリの変換と実行時に、オプションの関連エンティティが存在しない場合は、スローされるのではなく、その関連エンティティへのナビゲーションが無視されることが EF Core 保証されます。 ただし、コンパイラはこの EF Core 保証を認識しないため、LINQ クエリがメモリ内で実行されたかのように、LINQ to Objects を使用して警告を生成します。 結果として、null を許容しない演算子 (!) を使用して、実際の null 値が不可能であることをコンパイラに通知する必要があります。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

次のような場合にも同様の問題が発生します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

これを頻繁に実行し、問題のエンティティ型が EF Core クエリで主に (または排他的に) 使用されている場合は、ナビゲーションプロパティを null 非許容にし、Fluent API またはデータ注釈を使用してオプションとして構成することを検討してください。 これにより、リレーションシップを省略可能にしたまま、すべてのコンパイラ警告が削除されます。ただし、EF Core の外部でエンティティを走査した場合、プロパティには null 非許容として注釈が付けられますが、null 値を確認することができます。

## <a name="limitations"></a>制限事項

* リバースエンジニアリングでは、現在、 [ C# 8 つの null 値を許容する参照型 (nrts)](/dotnet/csharp/tutorials/nullable-reference-types)はサポートされていません。 EF Core は、機能が無効であると仮定するコードを常に生成C#します。 たとえば、null 値が許容されるテキスト列は、プロパティが必要かどうかを構成するために使用される Fluent API またはデータ注釈が `string?`ではなく `string` 型のプロパティとしてスキャフォールディングされます。 スキャフォールディングコードを編集し、null 値を許容C#する注釈に置き換えることができます。 Null 許容型参照型のスキャフォールディングサポートは、 [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)問題によって追跡されます。
* EF Core のパブリック API サーフェイスには、null 値の許容属性 (パブリック API が "無関係") に対してまだ注釈が付けられていないため、NRT 機能が有効になっているときに使用するのが困難な場合があります。 これには、特に、EF Core によって公開される非同期 LINQ 演算子 (たとえば、「」を含む) が含ま[れます。](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 5\.0 リリースでは、このことに対処する予定です。
