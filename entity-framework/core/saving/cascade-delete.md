---
title: "連鎖削除 - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
ms.technology: entity-framework-core
uid: core/saving/cascade-delete
ms.openlocfilehash: e1cb194d7c7472af59eb44fe2a084fa16c40c186
ms.sourcegitcommit: 3b21a7fdeddc7b3c70d9b7777b72bef61f59216c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="cascade-delete"></a><span data-ttu-id="6d05a-102">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="6d05a-102">Cascade Delete</span></span>

<span data-ttu-id="6d05a-103">連鎖削除は、関連する行の削除を自動的にトリガーする行の削除を許可する特性を記述するデータベース用語で通常使用されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-103">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="6d05a-104">EF コア削除動作覆われても、密接に関連する概念は、親との関係があるときに子エンティティの自動削除が切断されている--この i「孤立アイテムの削除」と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-104">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this i commonly known as "deleting orphans".</span></span>

<span data-ttu-id="6d05a-105">EF コアは、いくつかの別の削除動作を実装して、個々 のリレーションシップの削除の動作を構成できます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-105">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="6d05a-106">EF コアも実装して、[リレーションシップの requiredness] に基づいてリレーションシップごとに役立つ既定削除動作を自動的に構成している表記規則 (../modeling/relationships.md#required-and-optional-relationships)。</span><span class="sxs-lookup"><span data-stu-id="6d05a-106">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship] (../modeling/relationships.md#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="6d05a-107">動作を削除します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-107">Delete behaviors</span></span>
<span data-ttu-id="6d05a-108">削除の動作が定義されている、 *DeleteBehavior*列挙子を入力しに渡されることができます、 *OnDelete* fluent API を制御するかどうかの切断するプリンシパル/親エンティティの削除、依存する/子エンティティへのリレーションシップによっては、依存する/子エンティティに副作用が必要です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-108">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="6d05a-109">プリンシパル/親エンティティを削除または子へのリレーションシップが切断された場合に、EF は実行できる 3 つの操作です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-109">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>
* <span data-ttu-id="6d05a-110">子/依存ファイルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-110">The child/dependent can be deleted</span></span>
* <span data-ttu-id="6d05a-111">子の外部キーの値を設定することができますを null に</span><span class="sxs-lookup"><span data-stu-id="6d05a-111">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="6d05a-112">子が変更されません。</span><span class="sxs-lookup"><span data-stu-id="6d05a-112">The child remains unchanged</span></span>

> [!NOTE]  
> <span data-ttu-id="6d05a-113">EF の主要なモデルで構成されている、delete の動作は EF コアを使用してプリンシパル エンティティが削除され、依存エンティティは (つまり追跡対象の依存ファイル) のメモリに読み込まれるときにのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-113">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (i.e. for tracked dependents).</span></span> <span data-ttu-id="6d05a-114">対応するカスケード動作は、データベースは、コンテキストによって追跡されていないデータを確実にセットアップには、適用するために必要なアクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6d05a-114">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="6d05a-115">EF コアを使用して、データベースを作成するこの連鎖動作がセットアップするためになります。</span><span class="sxs-lookup"><span data-stu-id="6d05a-115">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="6d05a-116">2 番目のアクションの外部キーの値を null に設定が正しくない場合、外部キー値が許容されません。</span><span class="sxs-lookup"><span data-stu-id="6d05a-116">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="6d05a-117">(Null 非許容の外部キーは、必要なリレーションシップに相当) です。このような場合は、EF コアは、SaveChanges が呼び出されると、データベースに変更を永続化することはできませんのでれた時点で例外がスローされるまでに、外部キー プロパティが null としてマークされているを追跡します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-117">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="6d05a-118">データベースからの制約に違反することに似ています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-118">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="6d05a-119">動作を削除 4 つ以下の表に記載されています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-119">There are four delete behaviors, as listed in the tables below.</span></span> <span data-ttu-id="6d05a-120">省略可能なリレーションシップ (null 許容の外部キー) の場合、_は_null 外部キーの値、その結果、次の影響を保存します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-120">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="6d05a-121">動作名</span><span class="sxs-lookup"><span data-stu-id="6d05a-121">Behavior Name</span></span> | <span data-ttu-id="6d05a-122">メモリに依存する/子への影響</span><span class="sxs-lookup"><span data-stu-id="6d05a-122">Effect on dependent/child in memory</span></span> | <span data-ttu-id="6d05a-123">データベースに依存する/子への影響</span><span class="sxs-lookup"><span data-stu-id="6d05a-123">Effect on dependent/child in database</span></span>
|-|-|-
| <span data-ttu-id="6d05a-124">**Cascade**</span><span class="sxs-lookup"><span data-stu-id="6d05a-124">**Cascade**</span></span> | <span data-ttu-id="6d05a-125">エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-125">Entities are deleted</span></span> | <span data-ttu-id="6d05a-126">エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-126">Entities are deleted</span></span>
| <span data-ttu-id="6d05a-127">**ClientSetNull** (既定)</span><span class="sxs-lookup"><span data-stu-id="6d05a-127">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="6d05a-128">外部キー プロパティが設定を null に</span><span class="sxs-lookup"><span data-stu-id="6d05a-128">Foreign key properties are set to null</span></span> | <span data-ttu-id="6d05a-129">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-129">None</span></span>
| <span data-ttu-id="6d05a-130">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="6d05a-130">**SetNull**</span></span> | <span data-ttu-id="6d05a-131">外部キー プロパティが設定を null に</span><span class="sxs-lookup"><span data-stu-id="6d05a-131">Foreign key properties are set to null</span></span> | <span data-ttu-id="6d05a-132">外部キー プロパティが設定を null に</span><span class="sxs-lookup"><span data-stu-id="6d05a-132">Foreign key properties are set to null</span></span>
| <span data-ttu-id="6d05a-133">**制限します。**</span><span class="sxs-lookup"><span data-stu-id="6d05a-133">**Restrict**</span></span> | <span data-ttu-id="6d05a-134">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-134">None</span></span> | <span data-ttu-id="6d05a-135">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-135">None</span></span>

<span data-ttu-id="6d05a-136">必要なリレーションシップ (null 非許容の外部キー) は_いない_null 外部キーの値、その結果、次の影響を保存します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-136">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="6d05a-137">動作名</span><span class="sxs-lookup"><span data-stu-id="6d05a-137">Behavior Name</span></span> | <span data-ttu-id="6d05a-138">メモリに依存する/子への影響</span><span class="sxs-lookup"><span data-stu-id="6d05a-138">Effect on dependent/child in memory</span></span> | <span data-ttu-id="6d05a-139">データベースに依存する/子への影響</span><span class="sxs-lookup"><span data-stu-id="6d05a-139">Effect on dependent/child in database</span></span>
|-|-|-
| <span data-ttu-id="6d05a-140">**Cascade** (既定)</span><span class="sxs-lookup"><span data-stu-id="6d05a-140">**Cascade** (Default)</span></span> | <span data-ttu-id="6d05a-141">エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-141">Entities are deleted</span></span> | <span data-ttu-id="6d05a-142">エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-142">Entities are deleted</span></span>
| <span data-ttu-id="6d05a-143">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="6d05a-143">**ClientSetNull**</span></span> | <span data-ttu-id="6d05a-144">SaveChanges がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-144">SaveChanges throws</span></span> | <span data-ttu-id="6d05a-145">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-145">None</span></span>
| <span data-ttu-id="6d05a-146">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="6d05a-146">**SetNull**</span></span> | <span data-ttu-id="6d05a-147">SaveChanges がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-147">SaveChanges throws</span></span> | <span data-ttu-id="6d05a-148">SaveChanges がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-148">SaveChanges throws</span></span>
| <span data-ttu-id="6d05a-149">**制限します。**</span><span class="sxs-lookup"><span data-stu-id="6d05a-149">**Restrict**</span></span> | <span data-ttu-id="6d05a-150">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-150">None</span></span> | <span data-ttu-id="6d05a-151">なし</span><span class="sxs-lookup"><span data-stu-id="6d05a-151">None</span></span>

<span data-ttu-id="6d05a-152">上記の表で*None*制約違反が発生することができます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-152">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="6d05a-153">たとえば、プリンシパル/子エンティティが削除された場合は、依存する/子の外部キーを変更するアクションは実行されませんし、データベース可能性がありますがスローされます SaveChanges で外部制約違反のため。</span><span class="sxs-lookup"><span data-stu-id="6d05a-153">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="6d05a-154">レベルが高い場合。</span><span class="sxs-lookup"><span data-stu-id="6d05a-154">At a high level:</span></span>
* <span data-ttu-id="6d05a-155">存在するために、親エンティティが存在して、子を自動的に削除するために対処するための EF しを使用する場合*Cascade*です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-155">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="6d05a-156">通常、親は、なしに存在できないエンティティを必要なリレーションシップを使用して*Cascade*既定値です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-156">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="6d05a-157">処理する際は、外部キーがない、EF しを使用して、親がない可能性がありますまたは可能性のあるエンティティをした場合*ClientSetNull*</span><span class="sxs-lookup"><span data-stu-id="6d05a-157">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="6d05a-158">通常、親は、なしに存在可能なエンティティを省略可能なリレーションシップを使用して*ClientSetNull*既定値です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-158">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="6d05a-159">子の外部キーに null 値を伝達しようとするデータベースの場合と、子エンティティが読み込まれていない、を使用して*SetNull*です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-159">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="6d05a-160">ただし、データベースには、これをサポートする必要があり、次のようにデータベースを構成することができますと、その他の制限を実際に多くの場合、このオプションができなくなります。</span><span class="sxs-lookup"><span data-stu-id="6d05a-160">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="6d05a-161">その理由は*SetNull*は既定ではありません。</span><span class="sxs-lookup"><span data-stu-id="6d05a-161">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="6d05a-162">EF コアしれたエンティティを自動的に削除または外部キーに自動的には、null を使用したくない場合*Restrict*です。</span><span class="sxs-lookup"><span data-stu-id="6d05a-162">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="6d05a-163">注意が必要であること、コード子エンティティと、外部キーの値の同期を保つ手動でそれ以外の場合の制約の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-163">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="6d05a-164">EF6 とは異なり、EF コアで連鎖的な影響実行されないが、すぐに、代わりに SaveChanges が呼び出されたときだけです。</span><span class="sxs-lookup"><span data-stu-id="6d05a-164">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

> [!NOTE]  
> <span data-ttu-id="6d05a-165">**EF コア 2.0 での変更:**以前のリリースで*制限*原因となる省略可能な外部キーのプロパティを設定する追跡対象の依存エンティティは null、既定値は省略可能なリレーションシップの動作を削除します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-165">**Changes in EF Core 2.0:** In previous releases, *Restrict* would cause optional foreign key properties in tracked dependent entities to be set to null, and was the default delete behavior for optional relationships.</span></span> <span data-ttu-id="6d05a-166">EF コア 2.0 で、 *ClientSetNull*をその動作を表す導入され、省略可能なリレーションシップの既定値になりました。</span><span class="sxs-lookup"><span data-stu-id="6d05a-166">In EF Core 2.0, the *ClientSetNull* was introduced to represent that behavior and became the default for optional relationships.</span></span> <span data-ttu-id="6d05a-167">動作*Restrict*依存エンティティのすべての副作用を持たないは調整されました。</span><span class="sxs-lookup"><span data-stu-id="6d05a-167">The behavior of *Restrict* was adjusted to never have any side effects on dependent entities.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="6d05a-168">エンティティの削除の例</span><span class="sxs-lookup"><span data-stu-id="6d05a-168">Entity deletion examples</span></span>

<span data-ttu-id="6d05a-169">次のコードの一部である、[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)をダウンロードして実行できます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-169">The code below is part of a [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="6d05a-170">このサンプルでは、親エンティティが削除されたときの各オプションと必須の両方のリレーションシップの削除の動作の動作を示します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-170">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="6d05a-171">何が起こっているかを理解するには、各バリエーションについて説明しましょう。</span><span class="sxs-lookup"><span data-stu-id="6d05a-171">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="6d05a-172">必須またはオプションの関係を持つ DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="6d05a-172">DeleteBehavior.Cascade with required or optional relationship</span></span>

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

* <span data-ttu-id="6d05a-173">ブログが削除済みとしてマークされています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-173">Blog is marked as Deleted</span></span>
* <span data-ttu-id="6d05a-174">投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため</span><span class="sxs-lookup"><span data-stu-id="6d05a-174">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="6d05a-175">SaveChanges が両方の依存/子 (投稿) を次のプリンシパル/親 (ブログ) の削除を送信します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-175">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="6d05a-176">データベースから削除されているようになりましたので、保存した後のすべてのエンティティがデタッチ済み</span><span class="sxs-lookup"><span data-stu-id="6d05a-176">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="6d05a-177">DeleteBehavior.ClientSetNull または必要なリレーションシップを持つ DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="6d05a-177">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

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

* <span data-ttu-id="6d05a-178">ブログが削除済みとしてマークされています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-178">Blog is marked as Deleted</span></span>
* <span data-ttu-id="6d05a-179">投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため</span><span class="sxs-lookup"><span data-stu-id="6d05a-179">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="6d05a-180">Post FK を null に設定、SaveChanges が試行されますが、外部キーが null 許容ではないためにこれが失敗します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-180">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="6d05a-181">DeleteBehavior.ClientSetNull または省略可能なリレーションシップを持つ DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="6d05a-181">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

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

* <span data-ttu-id="6d05a-182">ブログが削除済みとしてマークされています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-182">Blog is marked as Deleted</span></span>
* <span data-ttu-id="6d05a-183">投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため</span><span class="sxs-lookup"><span data-stu-id="6d05a-183">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="6d05a-184">SaveChanges 試行を null に、プリンシパル/親 (ブログ) を削除する前に両方の依存/子供 (投稿) の外部キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-184">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="6d05a-185">保存した後、プリンシパル/親 (ブログ) 削除されますが、追跡されます dependents/子供 (投稿)</span><span class="sxs-lookup"><span data-stu-id="6d05a-185">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="6d05a-186">追跡対象の依存/子供 (投稿) ようになりました FK の null 値を持ち、親への参照、削除されたプリンシパル/(ブログ) は削除されました</span><span class="sxs-lookup"><span data-stu-id="6d05a-186">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="6d05a-187">必須またはオプションの関係を持つ DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="6d05a-187">DeleteBehavior.Restrict with required or optional relationship</span></span>

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

* <span data-ttu-id="6d05a-188">ブログが削除済みとしてマークされています。</span><span class="sxs-lookup"><span data-stu-id="6d05a-188">Blog is marked as Deleted</span></span>
* <span data-ttu-id="6d05a-189">投稿初期状態のまま Unchanged SaveChanges まで連鎖が発生しないため</span><span class="sxs-lookup"><span data-stu-id="6d05a-189">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="6d05a-190">*Restrict*自動的に、外部キーを null に設定、EF に指示して、null 以外は状態、保存せずに SaveChanges がスローされます</span><span class="sxs-lookup"><span data-stu-id="6d05a-190">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="6d05a-191">孤立ファイルの例の削除</span><span class="sxs-lookup"><span data-stu-id="6d05a-191">Delete orphans examples</span></span>

<span data-ttu-id="6d05a-192">次のコードの一部である、[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/)実行をダウンロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-192">The code below is part of a [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/CascadeDelete/) that can be downloaded an run.</span></span> <span data-ttu-id="6d05a-193">このサンプルでは、親/プリンシパルとその子/依存オブジェクト間のリレーションシップが切断されたときの各オプションと必須の両方のリレーションシップの削除の動作の動作を示します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-193">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="6d05a-194">この例では、リレーションシップは、プリンシパル/親 (ブログ) のコレクション ナビゲーション プロパティから依存ファイル/子供 (投稿) を削除することによって切断されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-194">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="6d05a-195">ただし、動作は代わりに依存する/子からのプリンシパル/親への参照は null にする場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="6d05a-195">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="6d05a-196">何が起こっているかを理解するには、各バリエーションについて説明しましょう。</span><span class="sxs-lookup"><span data-stu-id="6d05a-196">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="6d05a-197">必須またはオプションの関係を持つ DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="6d05a-197">DeleteBehavior.Cascade with required or optional relationship</span></span>

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

* <span data-ttu-id="6d05a-198">Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-198">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="6d05a-199">FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも</span><span class="sxs-lookup"><span data-stu-id="6d05a-199">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="6d05a-200">SaveChanges は、dependents/子供 (投稿) の削除を送信します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-200">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="6d05a-201">データベースから削除されているようになりましたので、保存した後、依存オブジェクト/子供 (投稿) がデタッチされました。</span><span class="sxs-lookup"><span data-stu-id="6d05a-201">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="6d05a-202">DeleteBehavior.ClientSetNull または必要なリレーションシップを持つ DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="6d05a-202">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

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

* <span data-ttu-id="6d05a-203">Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-203">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="6d05a-204">FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも</span><span class="sxs-lookup"><span data-stu-id="6d05a-204">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="6d05a-205">Post FK を null に設定、SaveChanges が試行されますが、外部キーが null 許容ではないためにこれが失敗します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-205">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="6d05a-206">DeleteBehavior.ClientSetNull または省略可能なリレーションシップを持つ DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="6d05a-206">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

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

* <span data-ttu-id="6d05a-207">Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-207">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="6d05a-208">FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも</span><span class="sxs-lookup"><span data-stu-id="6d05a-208">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="6d05a-209">SaveChanges を null に両方の依存/子供 (投稿) の外部キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6d05a-209">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="6d05a-210">保存した後 dependents/子供 (投稿) ようになりました FK の null 値を持ち、親への参照、削除されたプリンシパル/(ブログ) は削除されました</span><span class="sxs-lookup"><span data-stu-id="6d05a-210">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="6d05a-211">必須またはオプションの関係を持つ DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="6d05a-211">DeleteBehavior.Restrict with required or optional relationship</span></span>

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

* <span data-ttu-id="6d05a-212">Null としてマークする外部キー リレーションシップを切断する原因となったために、投稿が変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-212">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="6d05a-213">FK が null 許容でない場合、実際の値は変わりません null に設定されている場合でも</span><span class="sxs-lookup"><span data-stu-id="6d05a-213">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="6d05a-214">*Restrict*自動的に、外部キーを null に設定、EF に指示して、null 以外は状態、保存せずに SaveChanges がスローされます</span><span class="sxs-lookup"><span data-stu-id="6d05a-214">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="6d05a-215">追跡対象でないエンティティにカスケード</span><span class="sxs-lookup"><span data-stu-id="6d05a-215">Cascading to untracked entities</span></span>

<span data-ttu-id="6d05a-216">呼び出すと*SaveChanges*、cascade delete ルールは、コンテキストによって追跡されているすべてのエンティティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-216">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="6d05a-217">これは、すべての前に示した例では、これが理由をプリンシパル/親 (ブログ) とすべての依存/の子 (投稿) の両方を削除する SQL が生成されました。</span><span class="sxs-lookup"><span data-stu-id="6d05a-217">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="6d05a-218">プリンシパルが読み込まれる--だけの場合など、クエリが行われる場合せずブログ、`Include(b => b.Posts)`投稿--含めることも、SaveChanges のみが生成されますをプリンシパル/親を削除する SQL:</span><span class="sxs-lookup"><span data-stu-id="6d05a-218">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="6d05a-219">依存オブジェクトまたは子 (投稿) は、データベースが構成されている対応するカスケード動作にのみ削除されます。</span><span class="sxs-lookup"><span data-stu-id="6d05a-219">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="6d05a-220">EF を使用してデータベースを作成すると、この連鎖動作がセットアップするためになります。</span><span class="sxs-lookup"><span data-stu-id="6d05a-220">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
