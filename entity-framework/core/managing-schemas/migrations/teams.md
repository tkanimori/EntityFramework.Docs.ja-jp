---
title: チームの環境の EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 40cbc1c1bb0273bf733fadb884bffadcceeb162b
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
ms.locfileid: "26053802"
---
<a name="migrations-in-team-environments"></a><span data-ttu-id="e88e5-102">チーム環境での移行</span><span class="sxs-lookup"><span data-stu-id="e88e5-102">Migrations in Team Environments</span></span>
===============================
<span data-ttu-id="e88e5-103">チーム環境で移行を使用する場合、モデルのスナップショット ファイルに余分な注意してください。</span><span class="sxs-lookup"><span data-stu-id="e88e5-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="e88e5-104">このファイルがわかりますチームメイトの移行をマージ クリーンのして共有する前に、移行を再作成して競合を解決する必要がある場合。</span><span class="sxs-lookup"><span data-stu-id="e88e5-104">This file can tell you if your teammate's migration merges cleanly with yours of if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

<a name="merging"></a><span data-ttu-id="e88e5-105">マージ</span><span class="sxs-lookup"><span data-stu-id="e88e5-105">Merging</span></span>
-------
<span data-ttu-id="e88e5-106">他のチーム メンバーからの移行をマージする場合は、モデルは、スナップショット ファイルでの競合を取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e88e5-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="e88e5-107">両方の変更に関連性がない場合、マージが自明と 2 つの移行に共存させることができます。</span><span class="sxs-lookup"><span data-stu-id="e88e5-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="e88e5-108">たとえば、次のような顧客エンティティ型構成にマージの競合を取得することがあります。</span><span class="sxs-lookup"><span data-stu-id="e88e5-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

<span data-ttu-id="e88e5-109">これらのプロパティは、最終的なモデルに存在する必要があります、ために、両方のプロパティを追加することで、マージを完了します。</span><span class="sxs-lookup"><span data-stu-id="e88e5-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="e88e5-110">多くの場合、バージョン管理システムはのような変更を自動的にマージ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e88e5-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="e88e5-111">このような場合は、チームメイトの移行と移行は、互いに依存しません。</span><span class="sxs-lookup"><span data-stu-id="e88e5-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="e88e5-112">それらのいずれかの適用できない場合、最初からチームと共有する前に、移行に、追加の変更を加える必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e88e5-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

<a name="resolving-conflicts"></a><span data-ttu-id="e88e5-113">競合の解決</span><span class="sxs-lookup"><span data-stu-id="e88e5-113">Resolving conflicts</span></span>
-------------------
<span data-ttu-id="e88e5-114">場合もありますが発生する場合は true。 競合モデルのスナップショットをマージする場合。</span><span class="sxs-lookup"><span data-stu-id="e88e5-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="e88e5-115">たとえば、チームメイトとする可能性がありますそれぞれ名前を変更した同じプロパティです。</span><span class="sxs-lookup"><span data-stu-id="e88e5-115">For example, you and your teammate may each have renamed the same property.</span></span>

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

<span data-ttu-id="e88e5-116">この種の競合が発生した場合は、移行を再作成して解決します。</span><span class="sxs-lookup"><span data-stu-id="e88e5-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="e88e5-117">この場合は、以下の手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="e88e5-117">Follow these steps:</span></span>

1. <span data-ttu-id="e88e5-118">マージ変換およびマージする前に自分の作業ディレクトリへのロールバックを中止します。</span><span class="sxs-lookup"><span data-stu-id="e88e5-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="e88e5-119">移行を削除する (ただし、モデル変更を保持)</span><span class="sxs-lookup"><span data-stu-id="e88e5-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="e88e5-120">自分の作業ディレクトリにチームメイトの変更をマージします。</span><span class="sxs-lookup"><span data-stu-id="e88e5-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="e88e5-121">移行を再度追加します。</span><span class="sxs-lookup"><span data-stu-id="e88e5-121">Re-add your migration</span></span>

<span data-ttu-id="e88e5-122">その後で、2 つの移行は、正しい順序で適用できます。</span><span class="sxs-lookup"><span data-stu-id="e88e5-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="e88e5-123">移行は、最初に適用する列の名前を変更する*エイリアス*、その後、移行の名前を変更して*Username*です。</span><span class="sxs-lookup"><span data-stu-id="e88e5-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="e88e5-124">移行には、チームの残りの部分と安全に共有できます。</span><span class="sxs-lookup"><span data-stu-id="e88e5-124">Your migration can safely be shared with the rest of the team.</span></span>
