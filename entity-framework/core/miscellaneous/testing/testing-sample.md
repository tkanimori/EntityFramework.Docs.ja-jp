---
title: テストサンプルの EF Core-EF Core
description: EF Core を使用するアプリケーションをテストする方法を示すサンプル
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: dda7191df7646aa06aab51d8d7891bd0ba155674
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564260"
---
# <a name="ef-core-testing-sample"></a>EF Core テストのサンプル

> [!TIP]
> このドキュメントのコードは、実行可能な[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)として GitHub にあります。
> これらのテストの一部**は失敗する**ことに注意してください。 この理由については、以下で説明します。 

このドキュメントでは、EF Core を使用するコードをテストするサンプルについて説明します。

## <a name="the-application"></a>アプリケーション

この[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)には、2つのプロジェクトが含まれています。
- ItemsWebApi: 1 つのコントローラーで[ASP.NET Core によってサポート](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)される非常に単純な Web API
- テスト: コントローラーをテストする[Xunit](https://xunit.net/)テストプロジェクト

### <a name="the-model-and-business-rules"></a>モデルとビジネスルール

この API をサポートするモデルには、 ItemsととTagsいう2つのエンティティ型があります。

* Items大文字と小文字を区別する名前とTagsのコレクションを指定します。
* 各Tagにはラベルと、 Itemに適用された回数を表すカウントがあります。
* 各Itemには、指定Tagされたラベルを持つが1つだけ必要です。
  * 同じラベルの付いた項目に複数回タグが付けられている場合、そのラベルを持つ既存のタグの数が、新しいタグではなくインクリメントされます。 
* を削除Itemすると、関連Tags付けられているすべてのが削除します。

#### <a name="the-item-entity-type"></a>Itemエンティティ型

`Item`エンティティ型:

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

とその構成は`DbContext.OnModelCreating`次のとおりです。

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

エンティティ型は、ドメインモデルとビジネスルールを反映するために使用できる方法に制約があることに注意してください。 特に次の点に違いがあります。
- 主キーは`_id`フィールドに直接マップされ、一般公開されません。
  - EF は、主キーの値と名前を受け入れるプライベートコンストラクターを検出して使用します。
- `Name`プロパティは読み取り専用で、コンストラクターでのみ設定されます。 
- Tagsは、任意の`IReadOnlyList<Tag>`変更を防ぐためにとして公開されます。
  - EF は、 `Tags`名前を一致`_tags`させることによって、プロパティをバッキングフィールドに関連付けます。 
  - メソッド`AddTag`は、タグラベルを受け取り、上で説明したビジネスルールを実装します。
    つまり、タグは新しいラベルに対してのみ追加されます。
    それ以外の場合は、既存のラベルのカウントがインクリメントされます。
- ナビゲーション`Tags`プロパティは多対一のリレーションシップ用に構成されています。
  - からTagへItemのナビゲーションプロパティは必要ありません。そのため、このプロパティは含まれません。
  - また、 Tagでは外部キープロパティが定義されていません。
    代わりに、EF によってプロパティがシャドウ状態で作成および管理されます。

#### <a name="the-tag-entity-type"></a>Tagエンティティ型

`Tag`エンティティ型:

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

とその構成は`DbContext.OnModelCreating`次のとおりです。

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

と同様ItemにTag 、では主キーが非`Label`表示になり、プロパティは読み取り専用になります。

### <a name="the-itemscontroller"></a>ItemsController

Web API コントローラーは非常に基本的なものです。
コンストラクターの挿入`DbContext`を通じて、依存関係の挿入コンテナーからを取得します。

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

このメソッドには、指定ItemsされItemた名前を持つすべてのまたはを取得するメソッドがあります。

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

これには、新しいItemを追加するメソッドがあります。

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

ラベルをItem使用してにタグを付けるメソッド。

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

とのすべての関連Item Tagsを削除するメソッドを次に示します。

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

ほとんどの検証とエラー処理は、煩雑さを減らすために削除されました。

## <a name="the-tests"></a>テスト

テストは、複数のデータベースプロバイダー構成を使用して実行するように構成されています。
* アプリケーションによって使用されるプロバイダーである SQL Server プロバイダー
* SQLite プロバイダー
* メモリ内 SQLite データベースを使用する SQLite プロバイダー
* EF のメモリ内データベースプロバイダー

これを実現するには、すべてのテストを基本クラスに配置し、これを継承して、各プロバイダーでテストします。

> [!TIP]
> LocalDB を使用していない場合は、SQL Server 接続文字列を変更する必要があります。

> [!TIP]
> メモリ内テストで SQLite を使用する方法については、「 [sqlite を使用したテスト](xref:core/miscellaneous/testing/sqlite)」を参照してください。 

次の2つのテストが失敗することが予想されます。
* `Can_remove_item_and_all_associated_tags`EF インメモリデータベースプロバイダーを使用して実行する場合
* `Can_add_item_differing_only_by_case`SQL Server プロバイダーで実行する場合

詳細については、以下で詳しく説明します。

### <a name="setting-up-and-seeding-the-database"></a>データベースの設定とシード処理

XUnit は、ほとんどのテストフレームワークと同様に、テストの実行ごとに新しいテストクラスのインスタンスを作成します。
また、XUnit は、指定されたテストクラス内のテストを並行して実行しません。 これは、テストコンストラクターでデータベースをセットアップして構成できることを意味し、各テストの既知の状態になります。

> [!TIP]
> このサンプルでは、テストごとにデータベースを再作成します。
> これは SQLite および EF のメモリ内データベースのテストに適していますが、SQL Server を含む他のデータベースシステムでは大きなオーバーヘッドが発生する可能性があります。
> このオーバーヘッドを軽減するためのアプローチについては、「[複数のテストでデータベースを共有](xref:core/miscellaneous/testing/sharing-databases)する

各テストが実行されたとき:
* DbContextOptions は、使用中のプロバイダーに対して構成され、基底クラスのコンストラクターに渡されます。
  * これらのオプションはプロパティに格納され、DbContext インスタンスを作成するためのテスト全体で使用されます。
* シードメソッドが呼び出され、データベースを作成およびシードします。
  * Seed メソッドは、データベースを削除してから再作成することにより、データベースをクリーンアップします。
  * よく知られているいくつかのテストエンティティが作成され、データベースに保存されます。

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

各具象テストクラスは、このから継承します。
次に例を示します。

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>テスト構造

アプリケーションで依存関係の挿入が使用されている場合でも、テストは行われません。
ここでは依存関係の挿入を使用することはできますが、必要な追加のコードはほとんど価値がありません。
代わりに、を使用して`new` dbcontext が作成され、その後、依存関係としてコントローラーに直接渡されます。

各テストでは、コントローラーでテスト対象のメソッドが実行され、結果が想定どおりにアサートされます。
次に例を示します。

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

データベースのシードとテストの実行には、異なる DbContext インスタンスが使用されていることに注意してください。 これにより、シード処理時にコンテキストによって追跡されるエンティティがテストで使用されないようになります。
また、web アプリとサービスの動作にも適しています。

データベースを変化させるテストでは、同様の理由により、テストで2番目の DbContext インスタンスを作成します。
つまり、変更が実際にデータベースに保存されていることを確認するために、新しいコンテキストを作成し、それをデータベースから読み取ります。 次に例を示します。

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

もう少し複雑なテストでは、タグの追加に関するビジネスロジックについて説明します。

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>さまざまなデータベースプロバイダーを使用する場合の問題

実稼働アプリケーションで使用されているものとは異なるデータベースシステムを使用してテストすると、問題が発生する可能性があります。
これらの詳細については、 [EF Core を使用するコードのテスト](xref:core/miscellaneous/testing/index)に関する概念レベルで説明されています。  
以下のセクションでは、このサンプルのテストで示されている問題の2つの例について説明します。

### <a name="test-passes-when-application-is-broken"></a>アプリケーションが破損した場合のテスト成功

アプリケーションの要件の1つは、"Items大文字と小文字を区別した名前とのTagsコレクションを持つ" ということです。
これは非常に簡単にテストできます。

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

EF インメモリデータベースに対してこのテストを実行すると、すべての問題があることが示されます。
SQLite を使用すると、すべての問題が解決します。
ただし、SQL Server に対して実行した場合、テストは失敗します。

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

これは、EF のメモリ内データベースと SQLite データベースの両方で既定で大文字と小文字が区別されるためです。
一方、SQL Server では、大文字と小文字は区別されません。 

EF Core では、大文字と小文字の区別の変更によってパフォーマンスが大幅に低下する可能性があるので、設計上、これらの動作は変更されません。

これが問題であることを確認したら、アプリケーションを修正し、テストを補うことができます。
ただし、EF のインメモリデータベースまたは SQLite プロバイダーでテストする場合には、このバグが失われる可能性があります。

### <a name="test-fails-when-application-is-correct"></a>アプリケーションが正しい場合にテストが失敗する 

アプリケーションの要件の1つとして、「を削除Itemすると、関連Tags付けられているすべてのものを削除する必要がある」ということです。
ここでも、簡単にテストできます。

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

このテストは SQL Server と SQLite で成功しますが、EF インメモリデータベースでは失敗します。

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

この場合、SQL Server は[連鎖削除](xref:core/saving/cascade-delete)をサポートしているので、アプリケーションが正常に動作しています。 SQLite は、ほとんどのリレーショナルデータベースと同様に連鎖削除もサポートしているため、SQLite でこれをテストします。
一方、EF のメモリ内データベースは、[連鎖削除をサポートしていません](https://github.com/dotnet/efcore/issues/3924)。
つまり、アプリケーションのこの部分は、EF インメモリデータベースプロバイダーではテストできません。