---
title: 連鎖削除 - EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
ms.technology: entity-framework-core
uid: core/saving/cascade-delete
ms.openlocfilehash: 0fc8929c56d4c657b7fb1e3c8e4b1a71659220c9
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
---
# <a name="cascade-delete"></a>連鎖削除

連鎖削除は、関連する行の削除を自動的にトリガーする行の削除を許可する特性を記述するデータベース用語で通常使用されます。 EF コア削除動作覆われても密接に関連する概念は、親とのリレーションシップが切断されている--ときに子エンティティの自動削除をこれはよくと呼ばれます「孤立アイテムの削除」。

EF コアは、いくつかの別の削除動作を実装して、個々 のリレーションシップの削除の動作を構成できます。 EF コアもに基づいてリレーションシップごとに役立つ既定削除動作を自動的に構成している表記規則を実装、[リレーションシップの requiredness](../modeling/relationships.md#required-and-optional-relationships)です。

## <a name="delete-behaviors"></a>動作を削除します。
削除の動作が定義されている、 *DeleteBehavior*列挙子を入力しに渡されることができます、 *OnDelete* fluent API を制御するかどうかの切断するプリンシパル/親エンティティの削除、依存する/子エンティティへのリレーションシップによっては、依存する/子エンティティに副作用が必要です。

プリンシパル/親エンティティを削除または子へのリレーションシップが切断された場合に、EF は実行できる 3 つの操作です。
* 子/依存ファイルを削除することができます。
* 子の外部キーの値を設定することができますを null に
* 子が変更されません。

> [!NOTE]  
> EF の主要なモデルで構成されている、delete の動作は EF コアを使用してプリンシパル エンティティが削除され、依存エンティティは (つまり追跡対象の依存ファイル) のメモリに読み込まれるときにのみ適用されます。 対応するカスケード動作は、データベースは、コンテキストによって追跡されていないデータを確実にセットアップには、適用するために必要なアクションである必要があります。 EF コアを使用して、データベースを作成するこの連鎖動作がセットアップするためになります。

2 番目のアクションの外部キーの値を null に設定が正しくない場合、外部キー値が許容されません。 (Null 非許容の外部キーは、必要なリレーションシップに相当) です。このような場合は、EF コアは、SaveChanges が呼び出されると、データベースに変更を永続化することはできませんのでれた時点で例外がスローされるまでに、外部キー プロパティが null としてマークされているを追跡します。 データベースからの制約に違反することに似ています。

動作を削除 4 つ以下の表に記載されています。 省略可能なリレーションシップ (null 許容の外部キー) の場合、_は_null 外部キーの値、その結果、次の影響を保存します。

| 動作名               | メモリに依存する/子への影響    | データベースに依存する/子への影響  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascade**                 | エンティティが削除されます。                   | エンティティが削除されます。                   |
| **ClientSetNull** (既定) | 外部キー プロパティが設定を null に | なし                                   |
| **SetNull**                 | 外部キー プロパティが設定を null に | 外部キー プロパティが設定を null に |
| **制限します。**                | なし                                   | なし                                   |

必要なリレーションシップ (null 非許容の外部キー) は_いない_null 外部キーの値、その結果、次の影響を保存します。

| 動作名         | メモリに依存する/子への影響 | データベースに依存する/子への影響 |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (既定) | エンティティが削除されます。                | エンティティが削除されます。                  |
| **ClientSetNull**     | SaveChanges がスローされます。                  | なし                                  |
| **SetNull**           | SaveChanges がスローされます。                  | SaveChanges がスローされます。                    |
| **制限します。**          | なし                                | なし                                  |

上記の表で*None*制約違反が発生することができます。 たとえば、プリンシパル/子エンティティが削除された場合は、依存する/子の外部キーを変更するアクションは実行されませんし、データベース可能性がありますがスローされます SaveChanges で外部制約違反のため。

レベルが高い場合。
* 存在するために、親エンティティが存在して、子を自動的に削除するために対処するための EF しを使用する場合*Cascade*です。
  * 通常、親は、なしに存在できないエンティティを必要なリレーションシップを使用して*Cascade*既定値です。
* 処理する際は、外部キーがない、EF しを使用して、親がない可能性がありますまたは可能性のあるエンティティをした場合*ClientSetNull*
  * 通常、親は、なしに存在可能なエンティティを省略可能なリレーションシップを使用して*ClientSetNull*既定値です。
  * 子の外部キーに null 値を伝達しようとするデータベースの場合と、子エンティティが読み込まれていない、を使用して*SetNull*です。 ただし、データベースには、これをサポートする必要があり、次のようにデータベースを構成することができますと、その他の制限を実際に多くの場合、このオプションができなくなります。 その理由は*SetNull*は既定ではありません。
* EF コアしれたエンティティを自動的に削除または外部キーに自動的には、null を使用したくない場合*Restrict*です。 注意が必要であること、コード子エンティティと、外部キーの値の同期を保つ手動でそれ以外の場合の制約の例外がスローされます。

> [!NOTE]
> EF6 とは異なり、EF コアで連鎖的な影響実行されないが、すぐに、代わりに SaveChanges が呼び出されたときだけです。

> [!NOTE]  
> **EF コア 2.0 での変更:** 以前のリリースで*制限*原因となる省略可能な外部キーのプロパティを設定する追跡対象の依存エンティティは null、既定値は省略可能なリレーションシップの動作を削除します。 EF コア 2.0 で、 *ClientSetNull*をその動作を表す導入され、省略可能なリレーションシップの既定値になりました。 動作*Restrict*依存エンティティのすべての副作用を持たないは調整されました。

## <a name="entity-deletion-examples"></a>エンティティの削除の例

次のコードの一部である、[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)をダウンロードして実行できます。 このサンプルでは、親エンティティが削除されたときの各オプションと必須の両方のリレーションシップの削除の動作の動作を示します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

何が起こっているかを理解するには、各バリエーションについて説明しましょう。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>必須またはオプションの関係を持つ DeleteBehavior.Cascade

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* ブログが削除済みとしてマークされています。
* 投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため
* SaveChanges が両方の依存/子 (投稿) を次のプリンシパル/親 (ブログ) の削除を送信します。
* データベースから削除されているようになりましたので、保存した後のすべてのエンティティがデタッチ済み

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull または必要なリレーションシップを持つ DeleteBehavior.SetNull

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* ブログが削除済みとしてマークされています。
* 投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため
* Post FK を null に設定、SaveChanges が試行されますが、外部キーが null 許容ではないためにこれが失敗します。

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull または省略可能なリレーションシップを持つ DeleteBehavior.SetNull

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* ブログが削除済みとしてマークされています。
* 投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため
* SaveChanges 試行を null に、プリンシパル/親 (ブログ) を削除する前に両方の依存/子供 (投稿) の外部キーを設定します。
* 保存した後、プリンシパル/親 (ブログ) 削除されますが、追跡されます dependents/子供 (投稿)
* 追跡対象の依存/子供 (投稿) ようになりました FK の null 値を持ち、親への参照、削除されたプリンシパル/(ブログ) は削除されました

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>必須またはオプションの関係を持つ DeleteBehavior.Restrict

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* ブログが削除済みとしてマークされています。
* 投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため
* *Restrict*自動的に、外部キーを null に設定、EF に指示して、null 以外は状態、保存せずに SaveChanges がスローされます

## <a name="delete-orphans-examples"></a>孤立ファイルの例の削除

次のコードの一部である、[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)実行をダウンロードすることができます。 このサンプルでは、親/プリンシパルとその子/依存オブジェクト間のリレーションシップが切断されたときの各オプションと必須の両方のリレーションシップの削除の動作の動作を示します。 この例では、リレーションシップは、プリンシパル/親 (ブログ) のコレクション ナビゲーション プロパティから依存ファイル/子供 (投稿) を削除することによって切断されます。 ただし、動作は代わりに依存する/子からのプリンシパル/親への参照は null にする場合と同じです。

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

何が起こっているかを理解するには、各バリエーションについて説明しましょう。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>必須またはオプションの関係を持つ DeleteBehavior.Cascade

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
```

* Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。
  * FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも
* SaveChanges は、dependents/子供 (投稿) の削除を送信します。
* データベースから削除されているようになりましたので、保存した後、依存オブジェクト/子供 (投稿) がデタッチされました。

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull または必要なリレーションシップを持つ DeleteBehavior.SetNull

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。
  * FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも
* Post FK を null に設定、SaveChanges が試行されますが、外部キーが null 許容ではないためにこれが失敗します。

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull または省略可能なリレーションシップを持つ DeleteBehavior.SetNull

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。
  * FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも
* SaveChanges を null に両方の依存/子供 (投稿) の外部キーを設定します。
* 保存した後 dependents/子供 (投稿) ようになりました FK の null 値を持ち、親への参照、削除されたプリンシパル/(ブログ) は削除されました

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>必須またはオプションの関係を持つ DeleteBehavior.Restrict

```
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '1' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。
  * FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも
* *Restrict*自動的に、外部キーを null に設定、EF に指示して、null 以外は状態、保存せずに SaveChanges がスローされます

## <a name="cascading-to-untracked-entities"></a>追跡対象でないエンティティにカスケード

呼び出すと*SaveChanges*、cascade delete ルールは、コンテキストによって追跡されているすべてのエンティティに適用されます。 これは、すべての前に示した例では、これが理由をプリンシパル/親 (ブログ) とすべての依存/の子 (投稿) の両方を削除する SQL が生成されました。

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

プリンシパルが読み込まれる--だけの場合など、クエリが行われる場合せずブログ、`Include(b => b.Posts)`投稿--含めることも、SaveChanges のみが生成されますをプリンシパル/親を削除する SQL:

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

依存オブジェクトまたは子 (投稿) は、データベースが構成されている対応するカスケード動作にのみ削除されます。 EF を使用してデータベースを作成すると、この連鎖動作がセットアップするためになります。
