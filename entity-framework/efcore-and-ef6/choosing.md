---
title: "EF Core と EF6 - 自社に適しているのはどちらか"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: 9a113e0965fa75a03510199fb75165f6e9be0bbd
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a><span data-ttu-id="d6d99-102">EF Core と EF6 - 自社に適しているのはどちらか</span><span class="sxs-lookup"><span data-stu-id="d6d99-102">EF Core and EF6: Which One Is Right for You</span></span>

<span data-ttu-id="d6d99-103">次の情報は、Entity Framework Core と Entity Framework 6 のどちらを選ぶかを決める場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d6d99-103">The following information will help you choose between Entity Framework Core and Entity Framework 6.</span></span>

## <a name="guidance-for-new-applications"></a><span data-ttu-id="d6d99-104">新しいアプリケーションのガイダンス</span><span class="sxs-lookup"><span data-stu-id="d6d99-104">Guidance for new applications</span></span>

<span data-ttu-id="d6d99-105">EF Core は新製品で、重要な O/RM 機能がまだ一部欠けているため、今のところ多くのアプリケーションにとって EF6 が最適な選択となっています。</span><span class="sxs-lookup"><span data-stu-id="d6d99-105">Because EF Core is a new product, and still lacks some critical O/RM features, EF6 will still be the most suitable choice for many applications.</span></span>

<span data-ttu-id="d6d99-106">**次のタイプのアプリケーションでは、EF Core を使用されることをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="d6d99-106">**These are the types of applications we would recommend using EF Core for:**</span></span>

* <span data-ttu-id="d6d99-107">EF Core に未実装の機能を必要としないアプリケーション。</span><span class="sxs-lookup"><span data-stu-id="d6d99-107">New applications that do not require features that are not yet implemented in EF Core.</span></span> <span data-ttu-id="d6d99-108">EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6d99-108">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

* <span data-ttu-id="d6d99-109">ユニバーサル Windows プラットフォーム (UWP) アプリケーションや ASP.NET Core アプリケーションなど、.NET Core をターゲットとするアプリケーション。</span><span class="sxs-lookup"><span data-stu-id="d6d99-109">Applications that target .NET Core, such as Universal Windows Platform (UWP) and ASP.NET Core applications.</span></span> <span data-ttu-id="d6d99-110">EF6 では .NET Framework (.NET Framework 4.5 など) が必要なため、これらのアプリケーションは EF6 を使用できません。</span><span class="sxs-lookup"><span data-stu-id="d6d99-110">These applications can not use EF6 as it requires the .NET Framework (i.e. .NET Framework 4.5).</span></span>

## <a name="guidance-for-existing-ef6-applications"></a><span data-ttu-id="d6d99-111">既存の EF6 アプリケーションに関するガイダンス</span><span class="sxs-lookup"><span data-stu-id="d6d99-111">Guidance for existing EF6 applications</span></span>

<span data-ttu-id="d6d99-112">EF Core には根本的な変更が加えられているため、変更するやむを得ない理由がない限り、EF6 アプリケーションの EF Core への移行はお勧めできません。</span><span class="sxs-lookup"><span data-stu-id="d6d99-112">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span> <span data-ttu-id="d6d99-113">新機能を利用するために EF Core に移行する場合には、開始する前に制限事項を確認してください。</span><span class="sxs-lookup"><span data-stu-id="d6d99-113">If you want to move to EF Core to make use of new features, then make sure you are aware of its limitations before you start.</span></span> <span data-ttu-id="d6d99-114">EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6d99-114">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

<span data-ttu-id="d6d99-115">**EF6 から EF Core への移行は、アップグレードではなく移植と考える必要があります。**</span><span class="sxs-lookup"><span data-stu-id="d6d99-115">**You should view the move from EF6 to EF Core as a port rather than an upgrade.**</span></span> <span data-ttu-id="d6d99-116">詳細については、「[Porting from EF6 to EF Core (EF6 から EF Core への移植)](porting/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6d99-116">See [Porting from EF6 to EF Core](porting/index.md) for more information.</span></span>
