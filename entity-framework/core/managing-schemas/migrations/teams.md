---
title: チーム環境 - EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: e8ff7f468d5ab6dbd6285f1abf9199e413288d10
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997696"
---
<a name="migrations-in-team-environments"></a><span data-ttu-id="1e870-102">チーム環境での移行</span><span class="sxs-lookup"><span data-stu-id="1e870-102">Migrations in Team Environments</span></span>
===============================
<span data-ttu-id="1e870-103">チーム環境で移行を使用する場合、モデルのスナップショット ファイルに余分な注意してください。</span><span class="sxs-lookup"><span data-stu-id="1e870-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="1e870-104">このファイルは、あなたのチームメイトの移行が正常にマージされた場合、または共有する前に、移行を再作成して競合を解決する必要がある場合は通知します。</span><span class="sxs-lookup"><span data-stu-id="1e870-104">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

<a name="merging"></a><span data-ttu-id="1e870-105">マージ</span><span class="sxs-lookup"><span data-stu-id="1e870-105">Merging</span></span>
-------
<span data-ttu-id="1e870-106">他のチーム メンバーからの移行をマージすると、モデルのスナップショット ファイルの競合を取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e870-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="1e870-107">両方の変更が関連する場合は、マージは簡単ですし、2 つの移行に共存させることができます。</span><span class="sxs-lookup"><span data-stu-id="1e870-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="1e870-108">たとえば、次のような customer エンティティ型の構成でマージ競合を取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e870-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

<span data-ttu-id="1e870-109">これらのプロパティは、最終的なモデルに存在する必要があるために、両方のプロパティを追加することで、マージを完了します。</span><span class="sxs-lookup"><span data-stu-id="1e870-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="1e870-110">多くの場合、バージョン コントロール システムがこのような変更を自動的にマージ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e870-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="1e870-111">このような場合は、チームメイトの移行と移行は相互に依存しません。</span><span class="sxs-lookup"><span data-stu-id="1e870-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="1e870-112">最初にそれらのいずれかの適用でした後、は、チームと共有する前に、移行を追加、変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1e870-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

<a name="resolving-conflicts"></a><span data-ttu-id="1e870-113">競合の解決</span><span class="sxs-lookup"><span data-stu-id="1e870-113">Resolving conflicts</span></span>
-------------------
<span data-ttu-id="1e870-114">場合がありますモデルのスナップショットをマージするときに競合が発生します。</span><span class="sxs-lookup"><span data-stu-id="1e870-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="1e870-115">たとえば、チームメイトとする可能性がありますそれぞれ名前が変更、同じプロパティ。</span><span class="sxs-lookup"><span data-stu-id="1e870-115">For example, you and your teammate may each have renamed the same property.</span></span>

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

<span data-ttu-id="1e870-116">この種の競合が発生した場合は、移行を再作成して解決します。</span><span class="sxs-lookup"><span data-stu-id="1e870-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="1e870-117">この場合は、以下の手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="1e870-117">Follow these steps:</span></span>

1. <span data-ttu-id="1e870-118">マージ変換およびマージする前に、作業ディレクトリへのロールバックを中止します。</span><span class="sxs-lookup"><span data-stu-id="1e870-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="1e870-119">移行を削除する (ただし、モデルの変更を保持)</span><span class="sxs-lookup"><span data-stu-id="1e870-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="1e870-120">自分の作業ディレクトリにチームメイトが変更をマージします。</span><span class="sxs-lookup"><span data-stu-id="1e870-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="1e870-121">移行をもう一度追加します。</span><span class="sxs-lookup"><span data-stu-id="1e870-121">Re-add your migration</span></span>

<span data-ttu-id="1e870-122">その後で、正しい順序で 2 つの移行を適用できます。</span><span class="sxs-lookup"><span data-stu-id="1e870-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="1e870-123">移行が最初に、適用されると、列の名前を変更する*エイリアス*、移行の名前を変更して、その後*Username*。</span><span class="sxs-lookup"><span data-stu-id="1e870-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="1e870-124">移行は、チームの残りの部分と安全に共有できます。</span><span class="sxs-lookup"><span data-stu-id="1e870-124">Your migration can safely be shared with the rest of the team.</span></span>
