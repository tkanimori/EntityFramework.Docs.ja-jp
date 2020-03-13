---
title: 接続解除エンティティ - EF Core
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
uid: core/saving/disconnected-entities
ms.openlocfilehash: 421531e68ac98c0553938f1c24892701f22fef3c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413655"
---
# <a name="disconnected-entities"></a>接続解除エンティティ

DbContext インスタンスでは、データベースから返されるエンティティを自動的に追跡します。 必要に応じて SaveChanges が呼び出され、データベースが更新されたときに、これらのエンティティに対して行われた変更が検出されます。 詳細については、「[Basic Save](basic.md)」(基本の保存) および「[Related Data](related-data.md)」(関連データ) を参照してください。

しかし、エンティティが 1 つのコンテキスト インスタンスを使って照会され、別のインスタンスを使って保存される場合があります。 これは、エンティティの照会、クライアントへの送信、変更、要求内でのサーバーへの返送、および保存が行われる Web アプリケーションなどの "接続解除" シナリオで、頻繁に発生します。 この場合、2 番目のコンテキスト インスタンスでは、エンティティが新しいか (挿入する必要がある) または既存か (更新する必要があるか) を把握する必要があります。

<!-- markdownlint-disable MD028 -->
> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/)は GitHub で確認できます。

> [!TIP]
> EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。 これを回避する最善の方法は、各作業単位に一時的なコンテキストを使用して、最初は空のコンテキストにエンティティをアタッチし、それらのエンティティを保存してから、コンテキストが消去および破棄されるようにすることです。
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>新しいエンティティの識別

### <a name="client-identifies-new-entities"></a>クライアントが新しいエンティティを識別する

最も処理しやすいのは、エンティティが新規か既存かをクライアントからサーバーに知らせるタイミングです。 たとえば、新しいエンティティを挿入するための要求は、多くの場合、既存のエンティティを更新するための要求とは異なります。

このセクションの以降の説明では、別の方法で挿入か更新かを判断する必要があるケースを取り上げます。

### <a name="with-auto-generated-keys"></a>自動生成キーを利用する

自動生成されたキーの値は、エンティティを挿入する必要があるか、または更新する必要があるかを判断するために使用されることが、よくあります。 キーが設定済みでない (つまり、null や 0 などの CLR 既定値をまだ保持している) 場合、エンティティは常に新規となり、挿入する必要があります。 一方、キー値が設定済みの場合、エンティティは常に既に事前保存済みであり、更新する必要があります。 つまり、キーが値を保持している場合、エンティティは照会され、クライアントに送信されて、更新されるようになります。

エンティティ型がわかっている場合、設定されていないキーのチェックは簡単です。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

しかし、EF でもエンティティ型やキーの種類をチェックするための組み込みの方法を備えています。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> エンティティがコンテキストによって追跡されると、エンティティが追加済みの状態になっていても、すぐにキーが設定されます。 これは、TrackGraph API を使用している場合など、エンティティのグラフを走査して各グラフで行う操作を決定する際に役立ちます。 キー値は、エンティティを追跡するために何らかの呼び出しが行われる "_前_" に、ここに示された方法でのみ使用する必要があります。

### <a name="with-other-keys"></a>他のキーを利用する

キー値が自動生成されない場合、他のいくつかのメカニズムが、新しいエンティティの識別に利用されます。 これを行うための一般的な方法として、次の 2 つがあります。

* エンティティに対してクエリを実行する
* クライアントからフラグを渡す

エンティティに対してクエリを実行するには、単純に Find メソッドを使用します。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

クライアントからフラグを渡す完全なコードの提示は、このドキュメントでは行いません。 Web アプリでは、多くの場合、さまざまなアクションに対して異なる要求を行うか、または要求内で何らかの状態を渡して、コントローラーでその状態を抽出することになります。

## <a name="saving-single-entities"></a>単一のエンティティを保存する

挿入または更新のどちらが必要かがわかったら、次のように Add または Update を適切に使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

ただし、エンティティが自動生成されたキー値を使用する場合は、両方のケースで Update メソッドを使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Update メソッドは通常、挿入ではなく、更新用のエンティティをマークします。 ただし、エンティティが自動生成されたキーを保持しており、設定済みのキー値がない場合、そのエンティティは更新用ではなく、自動的に挿入用にマークされます。

> [!TIP]  
> この動作は、EF Core 2.0 で導入されました。 それ以前のリリースでは、Add または Update のどちらかを常に明示的に選択する必要があります。

エンティティが自動生成されたキーを使用していない場合、アプリケーションでは、エンティティの挿入または更新のどちらが必要かを、次のように判断する必要があります。次に例を示します。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

この場合の手順は、次のようになります。

* Find で null が返された場合、データベースがこの ID のブログを既に含んでいるわけではないため、Add を呼び出して挿入用にマークします。
* Find がエンティティを返した場合は、エンティティがデータベースに存在しており、コンテキストは既存のエンティティを追跡するようになります。
  * SetValues を使用して、このエンティティ上のすべてのプロパティの値を、クライアントから受信したプロパティに設定します。
  * SetValues の呼び出しでは、必要に応じて更新されるエンティティをマークします。

> [!TIP]  
> SetValues は、追跡されたエンティティのプロパティに別の値を保持しているプロパティを、変更済みとしてマークすることしか行いません。 これは、更新が送信されると、実際に変更されたそれらの列のみが更新されることを意味します (また、何も変更されなかった場合は、更新もまったく送信されません)。

## <a name="working-with-graphs"></a>グラフを操作する

### <a name="identity-resolution"></a>識別子の解決

上述したように、EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。 グラフを操作するとき、この不変の条件が維持されるようにグラフが理想的に作成される必要があり、コンテキストは 1 つの作業単位のみに対して使用される必要があります。 グラフに重複値が含まれている場合、EF に送信して複数のインスタンスを 1 つに統合する前に、そのグラフを処理する必要があります。 インスタンスに競合する値やリレーションシップがある場合、この処理が複雑になるので、アプリケーションのパイプラインで競合の解決が発生しないように、できるだけすぐに複数の値の統合を実行する必要があります。

### <a name="all-newall-existing-entities"></a>すべての新規/既存のエンティティ

グラフの操作の例では、関連する投稿のコレクションと共にブログを挿入または更新しています。 グラフ内のすべてのエンティティが挿入される必要がある場合、またはすべてが更新される必要がある場合、プロセスは上述した単一のエンティティの場合と同じです。 たとえば、作成されたブログと投稿のグラフは次のよになります。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

また、次のように挿入できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

Add の呼び出しでは、ブログとすべての投稿が挿入されるようにマークします。

同様に、グラフ内のすべてのエンティティが更新される必要がある場合は、Update を使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

ブログとそのすべての投稿が、更新されるようにマークされます。

### <a name="mix-of-new-and-existing-entities"></a>新規と既存のエンティティの混合

自動生成されたキーを使用する場合、挿入を必要とするエンティティと更新を必要とするエンティティがグラフ内に混在していても、挿入と更新の両方に Update を再使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Update は、設定されたキー値がない場合は、グラフ、ブログ、または投稿内のエンティティを挿入用にマークし、その他のエンティティはすべて更新用にマークします。

前述のように、自動生成キーを使用しない場合は、クエリおよびいくつかの処理を使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>削除の処理

エンティティの不在は、エンティティが削除されていることを意味することがよくあるため、削除は慎重に扱う必要があります。 これに対処する方法の 1 つは、エンティティが実際に削除されるのではなく、削除としてマークされるように、"論理的な削除" を使用することです。 これで、削除は更新と同様になります。 論理的な削除は、[クエリ フィルター](xref:core/querying/filters)を使用して実装できます。

実際の削除では、一般的なパターンとしてクエリ パターンの拡張機能を使用して、本質的なグラフの差分特定を行います。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

内部的には、Add、Attach、および Update では、追加済み (挿入する)、変更済み (更新する)、変更なし (何もしない)、または削除済み (削除) としてマークする必要があるかどうかに関して各エンティティで行われる判断と共に、グラフ走査を使用します。 このメカニズムは TrackGraph API 経由で公開されています。 たとえば、クライアントがエンティティのグラフを返送するときに、処理方法を示したフラグが各エンティティ上に設定されていると仮定しましょう。 このフラグを処理するために、次のように TrackGraph を使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

例を簡単にするために、フラグはエンティティの一部分としてしか表示されていません。 フラグは通常、DTO の一部か、または要求に含まれている他の状態になります。
