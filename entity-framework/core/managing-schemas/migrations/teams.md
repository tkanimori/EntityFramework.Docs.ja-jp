---
title: チーム環境での移行-EF Core
description: Entity Framework Core を使用したチーム環境での移行の管理と競合の解決に関するベストプラクティス
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062504"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="dab9b-103">チーム環境での移行</span><span class="sxs-lookup"><span data-stu-id="dab9b-103">Migrations in Team Environments</span></span>

<span data-ttu-id="dab9b-104">チーム環境で移行を行う場合は、モデルスナップショットファイルに特に注意を払ってください。</span><span class="sxs-lookup"><span data-stu-id="dab9b-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="dab9b-105">このファイルを使用すると、チームメイトの移行が正常にマージされたかどうかや、共有前に移行を再作成して競合を解決する必要があるかどうかがわかります。</span><span class="sxs-lookup"><span data-stu-id="dab9b-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="dab9b-106">マージ</span><span class="sxs-lookup"><span data-stu-id="dab9b-106">Merging</span></span>

<span data-ttu-id="dab9b-107">チームメイトからの移行をマージすると、モデルスナップショットファイルで競合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dab9b-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="dab9b-108">両方の変更が関連付けられていない場合、マージは簡単で、2つの移行を共存させることができます。</span><span class="sxs-lookup"><span data-stu-id="dab9b-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="dab9b-109">たとえば、次のような customer エンティティ型構成では、マージの競合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dab9b-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="dab9b-110">これらの両方のプロパティが最終的なモデルに存在する必要があるため、両方のプロパティを追加してマージを完了します。</span><span class="sxs-lookup"><span data-stu-id="dab9b-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="dab9b-111">多くの場合、このような変更は、バージョン管理システムによって自動的にマージされることがあります。</span><span class="sxs-lookup"><span data-stu-id="dab9b-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="dab9b-112">このような場合、移行とチームメイトの移行は互いに独立しています。</span><span class="sxs-lookup"><span data-stu-id="dab9b-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="dab9b-113">これらのいずれかを最初に適用することができるため、チームと共有する前に、移行に対して追加の変更を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dab9b-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="dab9b-114">競合の解決</span><span class="sxs-lookup"><span data-stu-id="dab9b-114">Resolving conflicts</span></span>

<span data-ttu-id="dab9b-115">モデルスナップショットモデルをマージするときに、実際の競合が発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="dab9b-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="dab9b-116">たとえば、自分とチームメイトが、それぞれ同じプロパティの名前を変更したとします。</span><span class="sxs-lookup"><span data-stu-id="dab9b-116">For example, you and your teammate may each have renamed the same property.</span></span>

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="dab9b-117">この種類の競合が発生した場合は、移行を再作成して解決します。</span><span class="sxs-lookup"><span data-stu-id="dab9b-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="dab9b-118">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="dab9b-118">Follow these steps:</span></span>

1. <span data-ttu-id="dab9b-119">マージの前に、マージを中止して作業ディレクトリにロールバックします</span><span class="sxs-lookup"><span data-stu-id="dab9b-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="dab9b-120">移行を削除する (ただし、モデルの変更を保持する)</span><span class="sxs-lookup"><span data-stu-id="dab9b-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="dab9b-121">チームメイトの変更を作業ディレクトリにマージする</span><span class="sxs-lookup"><span data-stu-id="dab9b-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="dab9b-122">移行を再追加する</span><span class="sxs-lookup"><span data-stu-id="dab9b-122">Re-add your migration</span></span>

<span data-ttu-id="dab9b-123">その後、2つの移行を正しい順序で適用できます。</span><span class="sxs-lookup"><span data-stu-id="dab9b-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="dab9b-124">最初に移行が適用され、列の名前が *別名*に変更されます。その後、移行によって *ユーザー名*に名前が変更されます。</span><span class="sxs-lookup"><span data-stu-id="dab9b-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="dab9b-125">移行は、チームの他のメンバーと安全に共有できます。</span><span class="sxs-lookup"><span data-stu-id="dab9b-125">Your migration can safely be shared with the rest of the team.</span></span>
