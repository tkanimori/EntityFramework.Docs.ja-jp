---
title: "切断されているエンティティの EF コア"
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
ms.technology: entity-framework-core
uid: core/saving/disconnected-entities
ms.openlocfilehash: 0ea02876b9594d54c971a7b70fcf7ce591e56ba0
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="disconnected-entities"></a>切断されているエンティティ

DbContext インスタンスでは、データベースから返されるエンティティを自動的に追跡されます。 SaveChanges が呼び出され、必要に応じて、データベースが更新されるときに、これらのエンティティに加えられた変更を検出し、されます。 参照してください[基本的な保存](basic.md)と[関連データ](related-data.md)詳細についてはします。

ただし、場合によってエンティティを照会コンテキストの 1 つのインスタンスを使用して、別のインスタンスを使用して保存します。 これは、ここでエンティティはクエリを実行、クライアントに送信される、変更、要求でサーバーに送信されるおよび保存し、web アプリケーションなどの「切断済み」のシナリオでよく発生します。 ここでは、2 つ目のコンテキスト エンティティが新しいかどうかを知る (を挿入する) 必要がありますか (更新する必要があります) を既存をインスタンスします。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/)は GitHub で確認できます。

## <a name="identifying-new-entities"></a>新しいエンティティを識別します。

### <a name="client-identifies-new-entities"></a>クライアントが新しいエンティティを識別します。

最も単純なに対処するしている場合、エンティティは、新しいまたは既存かどうかをクライアントがサーバーに通知します。 たとえば、多くの場合、新しいエンティティを挿入する要求は、既存のエンティティを更新する要求を異なるです。

このセクションの残りの部分は、ケースをカバーして、その他の何らかの方法で挿入または更新するかどうか判断するために必要です。

### <a name="with-auto-generated-keys"></a>自動生成キーを持つ

自動的に生成されたキーの値は、エンティティを挿入または更新する必要があるかどうかを決定する多くの場合、使用できます。 キーが設定されて (つまり、まだ null、0 を返しなどの CLR の既定値)、エンティティが新しいである必要があるあり、挿入する必要があります。 その一方で、キーの値が設定されている場合、必要がありますが既に保存されている以前し更新が必要なようになりました。 つまり、キーの値が、し、エンティティの照会、クライアントに送信されるがようになりました戻ると更新します。

これは、エンティティ型がわかっている場合に設定されていないキーの確認簡単です。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewSimple)]

ただし、EF は、これを行うすべてのエンティティ型とキーの種類の機能を組み込むこともあります。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> キーは、場合でも、状態が Added では、エンティティのエンティティが、コンテキストによって追跡されるとすぐに設定されます。 これは、エンティティと TrackGraph API を使用する場合と同様、このような処理を決定のグラフを走査する場合に役立ちます。 キーの値は、次に示す方法でのみ使用する必要があります_する前に_エンティティを追跡するために、呼び出しが行われます。

### <a name="with-other-keys"></a>その他のキーを持つ

キーの値が自動的に生成されたいないときに、新しいエンティティを識別するには、その他の機構が必要です。 この 2 つの一般的な方法があります。
 * エンティティのクエリ
 * クライアントから、フラグを渡します

クエリを実行するエンティティのだけ検索メソッドを使用します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewQuery)]

クライアントからフラグを渡すため完全なコードを表示するには、このドキュメントの範囲を超えています。 Web アプリで通常意味をさまざまな操作は、別の要求を行うか、要求にいくつかの状態を渡すこと、コント ローラーに抽出します。

## <a name="saving-single-entities"></a>単一のエンティティの保存

場合は認識されているかどうか、insert または update は、必要な追加または更新のいずれかを適切に使用することができます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

ただし、エンティティは、自動生成されたキー値を使用する場合のどちらの場合、Update メソッドを使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Update メソッドは、通常、更新、挿入ではないエンティティをマークします。 ただし、エンティティが、自動生成されたキーとキーの値が設定されていない、エンティティは代わりに自動的に設定されていますし、次のように挿入します。

> [!TIP]  
> この動作は、EF コア 2.0 で導入されました。 以前のリリースは常に使用するために必要な明示的に追加] または [更新プログラムを選択します。

エンティティは、自動生成キーを使用していないかどうかは、アプリケーションは、エンティティを挿入または更新するかどうかを決定する必要があります。 例。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

ここに示す手順は次のとおりです。
* 検索を返します。 null の場合、データベースに含まれていないこの ID を持つブログおを呼び出すようにし、追加する場合は、挿入のためマークします。
* 検索では、エンティティが返された場合、データベース内に存在する、およびコンテキストは既存のエンティティを追跡してようになりました
  * SetValues を使用して、クライアントから得られたものには、このエンティティのすべてのプロパティの値を設定します。
  * SetValues 呼び出しでは、必要に応じて更新するエンティティをマークします。

> [!TIP]  
> 追跡対象エンティティの別の値を持つプロパティを変更済みとして SetValues にマークされるだけです。 これは、更新が送信されると、実際に変更された列のみが更新されることを意味します。 (と何が変更された場合、更新プログラムは送信されませんまったく)。

## <a name="working-with-graphs"></a>グラフの操作

### <a name="all-newall-existing-entities"></a>すべての新しいまたはすべて既存のエンティティ

グラフの操作の例を挿入またはその関連する投稿のコレクションと共にブログを更新するとします。 グラフ内のすべてのエンティティを挿入する、またはすべてを更新する、し、プロセスが単一のエンティティの上記と同じです。 たとえば、ブログと次のように作成された投稿のグラフ:

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

次のように挿入することができます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertGraph)]

追加の呼び出しは、ブログや挿入されるすべての投稿にマークされます。

同様に、グラフ内のすべてのエンティティを更新する必要がある場合、更新プログラムを使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#UpdateGraph)]

ブログとそのすべての投稿を更新するマークされます。

### <a name="mix-of-new-and-existing-entities"></a>新規および既存のエンティティのミックス

、自動生成キーを持つ更新もう一度使用できます挿入と更新の両方の場合でも、グラフには、さまざまなエンティティを挿入するを必要として更新の必要なものが含まれます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

更新プログラムは、グラフ、ブログまたは post の場合に、キーの値が設定されていない、更新プログラムの他のすべてのエンティティがマークされているときに挿入するため、すべてのエンティティとしてマークされます。

としてする前に、自動生成キーを使用していない場合、クエリといくつかの処理を使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>削除の処理

削除はされてから処理する複雑になることが多くの場合、エンティティがない場合では削除する必要があります。 これに対処する 1 つの方法では、「回復可能な削除」を使用して、エンティティが実際に削除されるのではなく削除済みとしてマークされているようです。 削除してから、更新プログラムと同じになります。 使用して実装できる回復可能な削除[フィルターをクエリ](xref:core/querying/filters)です。

場合は true。 削除は、一般的なパターンでは、クエリ パターンの拡張機能を使用して graph 相違では基本的には、何を実行するには例:

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

内部的には、追加、アタッチ、および Update を使用して graph トラバーサルを Unchanged にするかどうか、としてマークしておく (挿入) を追加、変更 (更新) を各エンティティに対して行われた判断 (何もしない)、または (削除) を削除します。 このメカニズムは TrackGraph API を介して公開されます。 たとえば、クライアントがエンティティのグラフを返信ときにいくつかフラグを設定の処理方法を示す各エンティティを行うことと仮定します。 TrackGraph はこのフラグを処理するために使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#TrackGraph)]

フラグは、わかりやすくするための例では、エンティティの一部としてのみ表示されます。 通常、フラグは DTO または要求に含まれるその他の状態の一部になります。
