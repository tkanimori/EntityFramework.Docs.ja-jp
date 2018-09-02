---
title: EF Core と EF6 - 自社に適しているのはどちらか
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: 83ae754f899b624d322c48e8de8432b65519546e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993834"
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a><span data-ttu-id="fb7a3-102">EF Core と EF6 - 自社に適しているのはどちらか</span><span class="sxs-lookup"><span data-stu-id="fb7a3-102">EF Core and EF6: Which One Is Right for You</span></span>

<span data-ttu-id="fb7a3-103">次の情報は、Entity Framework Core と Entity Framework 6 のどちらを選ぶかを決める場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-103">The following information will help you choose between Entity Framework Core and Entity Framework 6.</span></span>

## <a name="guidance-for-new-applications"></a><span data-ttu-id="fb7a3-104">新しいアプリケーションのガイダンス</span><span class="sxs-lookup"><span data-stu-id="fb7a3-104">Guidance for new applications</span></span>

<span data-ttu-id="fb7a3-105">EF Core のすべての機能を活用する必要があり、EF Core にまだ実装されていない任意の機能を自分のアプリケーションで必要としない場合は、新しいアプリケーションに EF Core を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-105">Consider using EF Core for new applications if you want to take advantage of the all the capabilities of EF Core and your application does not require any features that are not yet implemented in EF Core.</span></span>

<span data-ttu-id="fb7a3-106">EF6 には .NET Framework 4.0 (または以降のバージョン) が必要であり、Windows 上でのみサポートされますが (つまり、EF6 は現在 .NET Core では実行されず、その他のオペレーティング システムではサポートされません)、これらの制約を許容でき、アプリケーションに EF6 では利用できない EF Core の新機能が必要なければ、EF6 を引き続き新しいアプリケーションに選択することができます。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-106">EF6 requires .NET Framework 4.0 (or a later version) and is only supported on Windows (that is, EF6 does not currently run on .NET Core and is not supported in other operating systems), but it is still a viable choice for new applications as long those constraints are acceptable and the application does not require new features in EF Core that are not available to EF6.</span></span>

<span data-ttu-id="fb7a3-107">EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-107">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

## <a name="guidance-for-existing-ef6-applications"></a><span data-ttu-id="fb7a3-108">既存の EF6 アプリケーションに関するガイダンス</span><span class="sxs-lookup"><span data-stu-id="fb7a3-108">Guidance for existing EF6 applications</span></span>

<span data-ttu-id="fb7a3-109">EF Core には根本的な変更が加えられているため、変更するやむを得ない理由がない限り、EF6 アプリケーションの EF Core への移行はお勧めできません。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-109">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span> <span data-ttu-id="fb7a3-110">新機能を利用するために EF Core に移行する場合には、開始する前に制限事項を確認してください。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-110">If you want to move to EF Core to make use of new features, then make sure you are aware of its limitations before you start.</span></span> <span data-ttu-id="fb7a3-111">EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-111">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

<span data-ttu-id="fb7a3-112">**EF6 から EF Core への移行は、アップグレードではなく移植と考える必要があります。**</span><span class="sxs-lookup"><span data-stu-id="fb7a3-112">**You should view the move from EF6 to EF Core as a port rather than an upgrade.**</span></span> <span data-ttu-id="fb7a3-113">詳細については、「[Porting from EF6 to EF Core (EF6 から EF Core への移植)](porting/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fb7a3-113">See [Porting from EF6 to EF Core](porting/index.md) for more information.</span></span>
