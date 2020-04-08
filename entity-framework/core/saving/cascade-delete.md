---
title: 連鎖削除 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
uid: core/saving/cascade-delete
ms.openlocfilehash: 6e92b869d691d0224abf1997d9eb7ea035489c5d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413667"
---
# <a name="cascade-delete"></a>連鎖削除

連鎖削除は、一般的に使用されているデータベース用語です。関連する行の削除を自動的にトリガーするための、行の削除を可能にする特性を示します。 EF Core の削除動作にも含まれる密接に関連した概念として、親エンティティとのリレーションシップが切断されたときの子エンティティの自動削除があります。これは一般に "孤立の削除" と呼ばれます。

EF Core は複数の削除動作を実装しており、個々のリレーションシップの削除動作を構成できます。 また、EF Core は[リレーションシップの必要性](../modeling/relationships.md#required-and-optional-relationships)に基づいて、各リレーションシップに対して有用な既定の削除動作を自動的に構成する規則も実装しています。

## <a name="delete-behaviors"></a>削除動作

削除動作は、*DeleteBehavior* 列挙子型で定義されます。また、*OnDelete* fluent API に渡して、プリンシパル/親エンティティの削除または依存/子エンティティとのリレーションシップの切断が、依存/子エンティティに副作用を及ぼすかどうかを制御することができます。

プリンシパル/親エンティティが削除されたとき、またはその子とのリレーションシップが切断されたときに EF が実行する可能性があるアクションは次の 3 つです。

* 子/依存が削除される可能性があります
* 子の外部キー (FK) 値が null に設定される可能性があります
* 子は変更されません

> [!NOTE]  
> EF Core モデルに構成されている削除動作は、EF Core を使用してプリンシパル エンティティが削除され、依存エンティティがメモリ内に読み込まれている場合 (つまり、追跡されている依存エンティティの場合) にのみ適用されます。 対応する連鎖動作をデータベースに設定し、コンテキストによって追跡されていないデータに対して必要なアクションが適用されるようにする必要があります。 EF Core を使用してデータベースを作成すると、この連鎖動作が設定されます。

上記の 2 番目のアクションで、外部キーが Null 許容でない場合、外部キー値を null に設定する操作は無効になります (Null 許容ではない外部キーは、必須のリレーションシップと同等です)。このような場合、EF Core は、SaveChanges が呼び出されるまで外部キーのプロパティが null とマークされていたことを追跡します。このとき、変更をデータベースに永続化できないため、例外がスローされます。 これは、データベースから制約違反を受け取る場合と似ています。

次の表に示すように、削除動作は 4 つあります。

### <a name="optional-relationships"></a>省略可能なリレーションシップ

省略可能なリレーションシップ (Null 許容の外部キー) の場合、null の外部キー値を保存することが_できます_。その結果、次のような影響があります。

| 動作名               | メモリ内の依存/子への影響    | データベース内の依存/子への影響  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascade**                 | エンティティは削除されます                   | エンティティは削除されます                   |
| **ClientSetNull** (既定) | 外部キー プロパティは null に設定されます | None                                   |
| **SetNull**                 | 外部キー プロパティは null に設定されます | 外部キー プロパティは null に設定されます |
| **Restrict**                | None                                   | None                                   |

### <a name="required-relationships"></a>必須リレーションシップ

必須のリレーションシップ (Null 許容ではない外部キー) の場合、null の外部キー値を保存することが_できません_。その結果、次のような影響があります。

| 動作名         | メモリ内の依存/子への影響 | データベース内の依存/子への影響 |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (既定) | エンティティは削除されます                | エンティティは削除されます                  |
| **ClientSetNull**     | SaveChanges がスローされます                  | None                                  |
| **SetNull**           | SaveChanges がスローされます                  | SaveChanges がスローされます                    |
| **Restrict**          | None                                | None                                  |

上記の表の "*なし*" は制約違反を引き起こす可能性があります。 たとえば、プリンシパル/子エンティティが削除されても、依存/子の外部キーを変更するアクションが実行されない場合、データベースは外部制約違反のために SaveChanges をスローする可能性があります。

高レベルでは:

* 親なしでは存在できないエンティティがあり、EF で子を自動的に削除したい場合は、*Cascade* を使用します。
  * 通常、親なしでは存在できないエンティティは、必須のリレーションシップを使用します。この場合、*Cascade* が既定です。
* エンティティが親を持つ場合と持たない場合があり、EF で外部キーを自動機に null にする場合は、*ClientSetNull* を使用します。
  * 通常、親なしでは存在できるエンティティは、省略可能なリレーションシップを使用します。この場合、*ClientSetNull* が既定です。
  * 子エンティティが読み込まれていない場合でも、データベースで null 値が子外部キーに伝達されるようにするには、*SetNull* を使用します。 ただし、データベースがこれをサポートしている必要があります。また、このようにデータベースを構成すると、他の制限が生じる可能性があります。そのため、多くの場合、実際にはこのオプションは実用的ではありません。 これが *SetNull* が既定ではない理由です。
* エンティティの削除や外部キーを null にする処理を EF Core で自動実行しないようにするには、*Restrict* を使用します。 この場合、コードで子エンティティとその外部キー値を手動で継続的に同期させる必要があります。そうしないと、制約の例外がスローされます。

> [!NOTE]
> EF Core は EF6 とは異なり、連鎖の影響はすぐに発生するのではなく、SaveChanges が呼び出されたときにのみ発生します。

> [!NOTE]  
> **EF Core 2.0 の変更:** 以前のリリースでは、*Restrict* を使用すると、追跡されている依存エンティティの省略可能な外部キーのプロパティが null に設定されていました。これは、省略可能なリレーションシップの既定の削除動作でした。 EF Core 2.0 では、その動作を表す *ClientSetNull* が導入され、省略可能なリレーションシップの既定になりました。 *Restrict* の動作は、依存エンティティに対する副作用がないように調整されました。

## <a name="entity-deletion-examples"></a>エンティティ削除の例

以下のコードは、ダウンロードして実行できる[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)の一部です。 このサンプルは、親エンティティが削除されたときに、省略可能なリレーションシップと必須のリレーションシップのそれぞれの削除動作で、何が起こるかを示しています。

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

各動作のサンプルを見て、何が起こるかを理解しましょう。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>必須または省略可能なリレーションシップがある DeleteBehavior.Cascade

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* ブログは Deleted とマークされています
* SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです
* SaveChanges は、両方の依存/子 (投稿) の削除、次にプリンシパル/親 (ブログ) の削除を送信します。
* 保存後、すべてのエンティティは、データベースから削除されているのでデタッチされます

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>必須のリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* ブログは Deleted とマークされています
* SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです
* SaveChanges は投稿 FK を null に設定しようとしますが、FK が Null 許容ではないため失敗します

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>省略可能なリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* ブログは Deleted とマークされています
* SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです
* SaveChanges は、プリンシパル/親 (ブログ) を削除する前に、依存/子 (投稿) 両方の FK を null に設定します
* 保存後、プリンシパル/親 (ブログ) は削除されますが、依存/子 (投稿) は引き続き追跡されます
* 追跡されている依存/子 (投稿) には null の FK 値があり、削除されたプリンシパル/親 (ブログ) への参照は削除されました

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>必須または省略可能なリレーションシップがある DeleteBehavior.Restrict

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* ブログは Deleted とマークされています
* SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです
* *Restrict* は、EF に対して FK を自動的に null に設定しないように指示しますが、Null 許容ではないままなので、SaveChanges は保存せずに例外をスローします

## <a name="delete-orphans-examples"></a>孤立の削除例

以下のコードは、ダウンロードして実行できる[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)の一部です。 このサンプルは、親/プリンシパルとその子/依存のリレーションシップが切断されたときに、省略可能なリレーションシップと必須のリレーションシップのそれぞれの削除動作で何が起こるかを示しています。 この例では、プリンシパル/親 (ブログ) のコレクション ナビゲーション プロパティから依存/子 (投稿) を削除することで、リレーションシップが切断されています。 ただし、依存/子からプリンシパル/親への参照が null に設定される場合、動作は同じです。

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

各動作のサンプルを見て、何が起こるかを理解しましょう。

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>必須または省略可能なリレーションシップがある DeleteBehavior.Cascade

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます
  * FK が Null 許容でない場合、null とマークされていても実際の値は変更されません
* SaveChanges は依存/子 (投稿) の削除を送信します
* 保存後、依存/子 (投稿) はデータベースから削除されているので、デタッチされます

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>必須のリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます
  * FK が Null 許容でない場合、null とマークされていても実際の値は変更されません
* SaveChanges は投稿 FK を null に設定しようとしますが、FK が Null 許容ではないため失敗します

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>省略可能なリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます
  * FK が Null 許容でない場合、null とマークされていても実際の値は変更されません
* SaveChanges は、両方の依存/子 (投稿) の FK を null に設定します
* 保存後、依存/子 (投稿) には null の FK 値があり、削除されたプリンシパル/親 (ブログ) への参照は削除されました

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>必須または省略可能なリレーションシップがある DeleteBehavior.Restrict

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます
  * FK が Null 許容でない場合、null とマークされていても実際の値は変更されません
* *Restrict* は、EF に対して FK を自動的に null に設定しないように指示しますが、Null 許容ではないままなので、SaveChanges は保存せずに例外をスローします

## <a name="cascading-to-untracked-entities"></a>追跡されていないエンティティに対する連鎖

*SaveChanges* を呼び出すと、コンテキストによっては、追跡されているすべてのエンティティに連鎖削除ルールが適用されます。 これは上記のすべての例で起こる状況です。プリンシパル/親 (ブログ) とすべての依存/子 (投稿) の両方を削除する SQL を生成したのは、このためです。

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

プリンシパルのみが読み込まれている場合 (たとえば、投稿に `Include(b => b.Posts)` がないブログに対してクエリが実行された場合など) にのみ、SaveChanges はプリンシパル/親を削除する SQL を生成します。

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

データベースに対応する連鎖動作が構成されている場合にのみ、依存者/子 (投稿) は削除されます。 EF を使用してデータベースを作成すると、この連鎖動作が設定されます。
