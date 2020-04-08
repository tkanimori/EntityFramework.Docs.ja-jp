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
# <a name="cascade-delete"></a><span data-ttu-id="d998e-102">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="d998e-102">Cascade Delete</span></span>

<span data-ttu-id="d998e-103">連鎖削除は、一般的に使用されているデータベース用語です。関連する行の削除を自動的にトリガーするための、行の削除を可能にする特性を示します。</span><span class="sxs-lookup"><span data-stu-id="d998e-103">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="d998e-104">EF Core の削除動作にも含まれる密接に関連した概念として、親エンティティとのリレーションシップが切断されたときの子エンティティの自動削除があります。これは一般に "孤立の削除" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d998e-104">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this is commonly known as "deleting orphans".</span></span>

<span data-ttu-id="d998e-105">EF Core は複数の削除動作を実装しており、個々のリレーションシップの削除動作を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d998e-105">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="d998e-106">また、EF Core は[リレーションシップの必要性](../modeling/relationships.md#required-and-optional-relationships)に基づいて、各リレーションシップに対して有用な既定の削除動作を自動的に構成する規則も実装しています。</span><span class="sxs-lookup"><span data-stu-id="d998e-106">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship](../modeling/relationships.md#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="d998e-107">削除動作</span><span class="sxs-lookup"><span data-stu-id="d998e-107">Delete behaviors</span></span>

<span data-ttu-id="d998e-108">削除動作は、*DeleteBehavior* 列挙子型で定義されます。また、*OnDelete* fluent API に渡して、プリンシパル/親エンティティの削除または依存/子エンティティとのリレーションシップの切断が、依存/子エンティティに副作用を及ぼすかどうかを制御することができます。</span><span class="sxs-lookup"><span data-stu-id="d998e-108">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="d998e-109">プリンシパル/親エンティティが削除されたとき、またはその子とのリレーションシップが切断されたときに EF が実行する可能性があるアクションは次の 3 つです。</span><span class="sxs-lookup"><span data-stu-id="d998e-109">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>

* <span data-ttu-id="d998e-110">子/依存が削除される可能性があります</span><span class="sxs-lookup"><span data-stu-id="d998e-110">The child/dependent can be deleted</span></span>
* <span data-ttu-id="d998e-111">子の外部キー (FK) 値が null に設定される可能性があります</span><span class="sxs-lookup"><span data-stu-id="d998e-111">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="d998e-112">子は変更されません</span><span class="sxs-lookup"><span data-stu-id="d998e-112">The child remains unchanged</span></span>

> [!NOTE]  
> <span data-ttu-id="d998e-113">EF Core モデルに構成されている削除動作は、EF Core を使用してプリンシパル エンティティが削除され、依存エンティティがメモリ内に読み込まれている場合 (つまり、追跡されている依存エンティティの場合) にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="d998e-113">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (that is, for tracked dependents).</span></span> <span data-ttu-id="d998e-114">対応する連鎖動作をデータベースに設定し、コンテキストによって追跡されていないデータに対して必要なアクションが適用されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d998e-114">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="d998e-115">EF Core を使用してデータベースを作成すると、この連鎖動作が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d998e-115">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="d998e-116">上記の 2 番目のアクションで、外部キーが Null 許容でない場合、外部キー値を null に設定する操作は無効になります</span><span class="sxs-lookup"><span data-stu-id="d998e-116">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="d998e-117">(Null 許容ではない外部キーは、必須のリレーションシップと同等です)。このような場合、EF Core は、SaveChanges が呼び出されるまで外部キーのプロパティが null とマークされていたことを追跡します。このとき、変更をデータベースに永続化できないため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d998e-117">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="d998e-118">これは、データベースから制約違反を受け取る場合と似ています。</span><span class="sxs-lookup"><span data-stu-id="d998e-118">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="d998e-119">次の表に示すように、削除動作は 4 つあります。</span><span class="sxs-lookup"><span data-stu-id="d998e-119">There are four delete behaviors, as listed in the tables below.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="d998e-120">省略可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="d998e-120">Optional relationships</span></span>

<span data-ttu-id="d998e-121">省略可能なリレーションシップ (Null 許容の外部キー) の場合、null の外部キー値を保存することが_できます_。その結果、次のような影響があります。</span><span class="sxs-lookup"><span data-stu-id="d998e-121">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="d998e-122">動作名</span><span class="sxs-lookup"><span data-stu-id="d998e-122">Behavior Name</span></span>               | <span data-ttu-id="d998e-123">メモリ内の依存/子への影響</span><span class="sxs-lookup"><span data-stu-id="d998e-123">Effect on dependent/child in memory</span></span>    | <span data-ttu-id="d998e-124">データベース内の依存/子への影響</span><span class="sxs-lookup"><span data-stu-id="d998e-124">Effect on dependent/child in database</span></span>  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| <span data-ttu-id="d998e-125">**Cascade**</span><span class="sxs-lookup"><span data-stu-id="d998e-125">**Cascade**</span></span>                 | <span data-ttu-id="d998e-126">エンティティは削除されます</span><span class="sxs-lookup"><span data-stu-id="d998e-126">Entities are deleted</span></span>                   | <span data-ttu-id="d998e-127">エンティティは削除されます</span><span class="sxs-lookup"><span data-stu-id="d998e-127">Entities are deleted</span></span>                   |
| <span data-ttu-id="d998e-128">**ClientSetNull** (既定)</span><span class="sxs-lookup"><span data-stu-id="d998e-128">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="d998e-129">外部キー プロパティは null に設定されます</span><span class="sxs-lookup"><span data-stu-id="d998e-129">Foreign key properties are set to null</span></span> | <span data-ttu-id="d998e-130">None</span><span class="sxs-lookup"><span data-stu-id="d998e-130">None</span></span>                                   |
| <span data-ttu-id="d998e-131">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="d998e-131">**SetNull**</span></span>                 | <span data-ttu-id="d998e-132">外部キー プロパティは null に設定されます</span><span class="sxs-lookup"><span data-stu-id="d998e-132">Foreign key properties are set to null</span></span> | <span data-ttu-id="d998e-133">外部キー プロパティは null に設定されます</span><span class="sxs-lookup"><span data-stu-id="d998e-133">Foreign key properties are set to null</span></span> |
| <span data-ttu-id="d998e-134">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="d998e-134">**Restrict**</span></span>                | <span data-ttu-id="d998e-135">None</span><span class="sxs-lookup"><span data-stu-id="d998e-135">None</span></span>                                   | <span data-ttu-id="d998e-136">None</span><span class="sxs-lookup"><span data-stu-id="d998e-136">None</span></span>                                   |

### <a name="required-relationships"></a><span data-ttu-id="d998e-137">必須リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="d998e-137">Required relationships</span></span>

<span data-ttu-id="d998e-138">必須のリレーションシップ (Null 許容ではない外部キー) の場合、null の外部キー値を保存することが_できません_。その結果、次のような影響があります。</span><span class="sxs-lookup"><span data-stu-id="d998e-138">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="d998e-139">動作名</span><span class="sxs-lookup"><span data-stu-id="d998e-139">Behavior Name</span></span>         | <span data-ttu-id="d998e-140">メモリ内の依存/子への影響</span><span class="sxs-lookup"><span data-stu-id="d998e-140">Effect on dependent/child in memory</span></span> | <span data-ttu-id="d998e-141">データベース内の依存/子への影響</span><span class="sxs-lookup"><span data-stu-id="d998e-141">Effect on dependent/child in database</span></span> |
|:----------------------|:------------------------------------|:--------------------------------------|
| <span data-ttu-id="d998e-142">**Cascade** (既定)</span><span class="sxs-lookup"><span data-stu-id="d998e-142">**Cascade** (Default)</span></span> | <span data-ttu-id="d998e-143">エンティティは削除されます</span><span class="sxs-lookup"><span data-stu-id="d998e-143">Entities are deleted</span></span>                | <span data-ttu-id="d998e-144">エンティティは削除されます</span><span class="sxs-lookup"><span data-stu-id="d998e-144">Entities are deleted</span></span>                  |
| <span data-ttu-id="d998e-145">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="d998e-145">**ClientSetNull**</span></span>     | <span data-ttu-id="d998e-146">SaveChanges がスローされます</span><span class="sxs-lookup"><span data-stu-id="d998e-146">SaveChanges throws</span></span>                  | <span data-ttu-id="d998e-147">None</span><span class="sxs-lookup"><span data-stu-id="d998e-147">None</span></span>                                  |
| <span data-ttu-id="d998e-148">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="d998e-148">**SetNull**</span></span>           | <span data-ttu-id="d998e-149">SaveChanges がスローされます</span><span class="sxs-lookup"><span data-stu-id="d998e-149">SaveChanges throws</span></span>                  | <span data-ttu-id="d998e-150">SaveChanges がスローされます</span><span class="sxs-lookup"><span data-stu-id="d998e-150">SaveChanges throws</span></span>                    |
| <span data-ttu-id="d998e-151">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="d998e-151">**Restrict**</span></span>          | <span data-ttu-id="d998e-152">None</span><span class="sxs-lookup"><span data-stu-id="d998e-152">None</span></span>                                | <span data-ttu-id="d998e-153">None</span><span class="sxs-lookup"><span data-stu-id="d998e-153">None</span></span>                                  |

<span data-ttu-id="d998e-154">上記の表の "*なし*" は制約違反を引き起こす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d998e-154">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="d998e-155">たとえば、プリンシパル/子エンティティが削除されても、依存/子の外部キーを変更するアクションが実行されない場合、データベースは外部制約違反のために SaveChanges をスローする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d998e-155">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="d998e-156">高レベルでは:</span><span class="sxs-lookup"><span data-stu-id="d998e-156">At a high level:</span></span>

* <span data-ttu-id="d998e-157">親なしでは存在できないエンティティがあり、EF で子を自動的に削除したい場合は、*Cascade* を使用します。</span><span class="sxs-lookup"><span data-stu-id="d998e-157">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="d998e-158">通常、親なしでは存在できないエンティティは、必須のリレーションシップを使用します。この場合、*Cascade* が既定です。</span><span class="sxs-lookup"><span data-stu-id="d998e-158">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="d998e-159">エンティティが親を持つ場合と持たない場合があり、EF で外部キーを自動機に null にする場合は、*ClientSetNull* を使用します。</span><span class="sxs-lookup"><span data-stu-id="d998e-159">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="d998e-160">通常、親なしでは存在できるエンティティは、省略可能なリレーションシップを使用します。この場合、*ClientSetNull* が既定です。</span><span class="sxs-lookup"><span data-stu-id="d998e-160">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="d998e-161">子エンティティが読み込まれていない場合でも、データベースで null 値が子外部キーに伝達されるようにするには、*SetNull* を使用します。</span><span class="sxs-lookup"><span data-stu-id="d998e-161">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="d998e-162">ただし、データベースがこれをサポートしている必要があります。また、このようにデータベースを構成すると、他の制限が生じる可能性があります。そのため、多くの場合、実際にはこのオプションは実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="d998e-162">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="d998e-163">これが *SetNull* が既定ではない理由です。</span><span class="sxs-lookup"><span data-stu-id="d998e-163">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="d998e-164">エンティティの削除や外部キーを null にする処理を EF Core で自動実行しないようにするには、*Restrict* を使用します。</span><span class="sxs-lookup"><span data-stu-id="d998e-164">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="d998e-165">この場合、コードで子エンティティとその外部キー値を手動で継続的に同期させる必要があります。そうしないと、制約の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d998e-165">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="d998e-166">EF Core は EF6 とは異なり、連鎖の影響はすぐに発生するのではなく、SaveChanges が呼び出されたときにのみ発生します。</span><span class="sxs-lookup"><span data-stu-id="d998e-166">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

> [!NOTE]  
> <span data-ttu-id="d998e-167">**EF Core 2.0 の変更:** 以前のリリースでは、*Restrict* を使用すると、追跡されている依存エンティティの省略可能な外部キーのプロパティが null に設定されていました。これは、省略可能なリレーションシップの既定の削除動作でした。</span><span class="sxs-lookup"><span data-stu-id="d998e-167">**Changes in EF Core 2.0:** In previous releases, *Restrict* would cause optional foreign key properties in tracked dependent entities to be set to null, and was the default delete behavior for optional relationships.</span></span> <span data-ttu-id="d998e-168">EF Core 2.0 では、その動作を表す *ClientSetNull* が導入され、省略可能なリレーションシップの既定になりました。</span><span class="sxs-lookup"><span data-stu-id="d998e-168">In EF Core 2.0, the *ClientSetNull* was introduced to represent that behavior and became the default for optional relationships.</span></span> <span data-ttu-id="d998e-169">*Restrict* の動作は、依存エンティティに対する副作用がないように調整されました。</span><span class="sxs-lookup"><span data-stu-id="d998e-169">The behavior of *Restrict* was adjusted to never have any side effects on dependent entities.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="d998e-170">エンティティ削除の例</span><span class="sxs-lookup"><span data-stu-id="d998e-170">Entity deletion examples</span></span>

<span data-ttu-id="d998e-171">以下のコードは、ダウンロードして実行できる[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)の一部です。</span><span class="sxs-lookup"><span data-stu-id="d998e-171">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="d998e-172">このサンプルは、親エンティティが削除されたときに、省略可能なリレーションシップと必須のリレーションシップのそれぞれの削除動作で、何が起こるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="d998e-172">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="d998e-173">各動作のサンプルを見て、何が起こるかを理解しましょう。</span><span class="sxs-lookup"><span data-stu-id="d998e-173">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="d998e-174">必須または省略可能なリレーションシップがある DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="d998e-174">DeleteBehavior.Cascade with required or optional relationship</span></span>

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

* <span data-ttu-id="d998e-175">ブログは Deleted とマークされています</span><span class="sxs-lookup"><span data-stu-id="d998e-175">Blog is marked as Deleted</span></span>
* <span data-ttu-id="d998e-176">SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです</span><span class="sxs-lookup"><span data-stu-id="d998e-176">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="d998e-177">SaveChanges は、両方の依存/子 (投稿) の削除、次にプリンシパル/親 (ブログ) の削除を送信します。</span><span class="sxs-lookup"><span data-stu-id="d998e-177">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="d998e-178">保存後、すべてのエンティティは、データベースから削除されているのでデタッチされます</span><span class="sxs-lookup"><span data-stu-id="d998e-178">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="d998e-179">必須のリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="d998e-179">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

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

* <span data-ttu-id="d998e-180">ブログは Deleted とマークされています</span><span class="sxs-lookup"><span data-stu-id="d998e-180">Blog is marked as Deleted</span></span>
* <span data-ttu-id="d998e-181">SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです</span><span class="sxs-lookup"><span data-stu-id="d998e-181">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="d998e-182">SaveChanges は投稿 FK を null に設定しようとしますが、FK が Null 許容ではないため失敗します</span><span class="sxs-lookup"><span data-stu-id="d998e-182">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="d998e-183">省略可能なリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="d998e-183">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

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

* <span data-ttu-id="d998e-184">ブログは Deleted とマークされています</span><span class="sxs-lookup"><span data-stu-id="d998e-184">Blog is marked as Deleted</span></span>
* <span data-ttu-id="d998e-185">SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです</span><span class="sxs-lookup"><span data-stu-id="d998e-185">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="d998e-186">SaveChanges は、プリンシパル/親 (ブログ) を削除する前に、依存/子 (投稿) 両方の FK を null に設定します</span><span class="sxs-lookup"><span data-stu-id="d998e-186">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="d998e-187">保存後、プリンシパル/親 (ブログ) は削除されますが、依存/子 (投稿) は引き続き追跡されます</span><span class="sxs-lookup"><span data-stu-id="d998e-187">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="d998e-188">追跡されている依存/子 (投稿) には null の FK 値があり、削除されたプリンシパル/親 (ブログ) への参照は削除されました</span><span class="sxs-lookup"><span data-stu-id="d998e-188">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="d998e-189">必須または省略可能なリレーションシップがある DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="d998e-189">DeleteBehavior.Restrict with required or optional relationship</span></span>

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

* <span data-ttu-id="d998e-190">ブログは Deleted とマークされています</span><span class="sxs-lookup"><span data-stu-id="d998e-190">Blog is marked as Deleted</span></span>
* <span data-ttu-id="d998e-191">SaveChanges まで連鎖が発生しないため、初期段階で投稿は Unchanged のままです</span><span class="sxs-lookup"><span data-stu-id="d998e-191">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="d998e-192">*Restrict* は、EF に対して FK を自動的に null に設定しないように指示しますが、Null 許容ではないままなので、SaveChanges は保存せずに例外をスローします</span><span class="sxs-lookup"><span data-stu-id="d998e-192">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="d998e-193">孤立の削除例</span><span class="sxs-lookup"><span data-stu-id="d998e-193">Delete orphans examples</span></span>

<span data-ttu-id="d998e-194">以下のコードは、ダウンロードして実行できる[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)の一部です。</span><span class="sxs-lookup"><span data-stu-id="d998e-194">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="d998e-195">このサンプルは、親/プリンシパルとその子/依存のリレーションシップが切断されたときに、省略可能なリレーションシップと必須のリレーションシップのそれぞれの削除動作で何が起こるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="d998e-195">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="d998e-196">この例では、プリンシパル/親 (ブログ) のコレクション ナビゲーション プロパティから依存/子 (投稿) を削除することで、リレーションシップが切断されています。</span><span class="sxs-lookup"><span data-stu-id="d998e-196">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="d998e-197">ただし、依存/子からプリンシパル/親への参照が null に設定される場合、動作は同じです。</span><span class="sxs-lookup"><span data-stu-id="d998e-197">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="d998e-198">各動作のサンプルを見て、何が起こるかを理解しましょう。</span><span class="sxs-lookup"><span data-stu-id="d998e-198">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="d998e-199">必須または省略可能なリレーションシップがある DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="d998e-199">DeleteBehavior.Cascade with required or optional relationship</span></span>

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

* <span data-ttu-id="d998e-200">リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます</span><span class="sxs-lookup"><span data-stu-id="d998e-200">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="d998e-201">FK が Null 許容でない場合、null とマークされていても実際の値は変更されません</span><span class="sxs-lookup"><span data-stu-id="d998e-201">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="d998e-202">SaveChanges は依存/子 (投稿) の削除を送信します</span><span class="sxs-lookup"><span data-stu-id="d998e-202">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="d998e-203">保存後、依存/子 (投稿) はデータベースから削除されているので、デタッチされます</span><span class="sxs-lookup"><span data-stu-id="d998e-203">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="d998e-204">必須のリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="d998e-204">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

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

* <span data-ttu-id="d998e-205">リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます</span><span class="sxs-lookup"><span data-stu-id="d998e-205">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="d998e-206">FK が Null 許容でない場合、null とマークされていても実際の値は変更されません</span><span class="sxs-lookup"><span data-stu-id="d998e-206">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="d998e-207">SaveChanges は投稿 FK を null に設定しようとしますが、FK が Null 許容ではないため失敗します</span><span class="sxs-lookup"><span data-stu-id="d998e-207">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="d998e-208">省略可能なリレーションシップがある DeleteBehavior.ClientSetNull または DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="d998e-208">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

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

* <span data-ttu-id="d998e-209">リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます</span><span class="sxs-lookup"><span data-stu-id="d998e-209">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="d998e-210">FK が Null 許容でない場合、null とマークされていても実際の値は変更されません</span><span class="sxs-lookup"><span data-stu-id="d998e-210">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="d998e-211">SaveChanges は、両方の依存/子 (投稿) の FK を null に設定します</span><span class="sxs-lookup"><span data-stu-id="d998e-211">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="d998e-212">保存後、依存/子 (投稿) には null の FK 値があり、削除されたプリンシパル/親 (ブログ) への参照は削除されました</span><span class="sxs-lookup"><span data-stu-id="d998e-212">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="d998e-213">必須または省略可能なリレーションシップがある DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="d998e-213">DeleteBehavior.Restrict with required or optional relationship</span></span>

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

* <span data-ttu-id="d998e-214">リレーションシップを切断すると FK は null とマークされるので、投稿は Modified とマークされます</span><span class="sxs-lookup"><span data-stu-id="d998e-214">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="d998e-215">FK が Null 許容でない場合、null とマークされていても実際の値は変更されません</span><span class="sxs-lookup"><span data-stu-id="d998e-215">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="d998e-216">*Restrict* は、EF に対して FK を自動的に null に設定しないように指示しますが、Null 許容ではないままなので、SaveChanges は保存せずに例外をスローします</span><span class="sxs-lookup"><span data-stu-id="d998e-216">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="d998e-217">追跡されていないエンティティに対する連鎖</span><span class="sxs-lookup"><span data-stu-id="d998e-217">Cascading to untracked entities</span></span>

<span data-ttu-id="d998e-218">*SaveChanges* を呼び出すと、コンテキストによっては、追跡されているすべてのエンティティに連鎖削除ルールが適用されます。</span><span class="sxs-lookup"><span data-stu-id="d998e-218">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="d998e-219">これは上記のすべての例で起こる状況です。プリンシパル/親 (ブログ) とすべての依存/子 (投稿) の両方を削除する SQL を生成したのは、このためです。</span><span class="sxs-lookup"><span data-stu-id="d998e-219">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="d998e-220">プリンシパルのみが読み込まれている場合 (たとえば、投稿に `Include(b => b.Posts)` がないブログに対してクエリが実行された場合など) にのみ、SaveChanges はプリンシパル/親を削除する SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="d998e-220">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="d998e-221">データベースに対応する連鎖動作が構成されている場合にのみ、依存者/子 (投稿) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="d998e-221">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="d998e-222">EF を使用してデータベースを作成すると、この連鎖動作が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d998e-222">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
