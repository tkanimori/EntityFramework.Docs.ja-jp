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
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a>EF Core と EF6 - 自社に適しているのはどちらか

次の情報は、Entity Framework Core と Entity Framework 6 のどちらを選ぶかを決める場合に役立ちます。

## <a name="guidance-for-new-applications"></a>新しいアプリケーションのガイダンス

EF Core は新製品で、重要な O/RM 機能がまだ一部欠けているため、今のところ多くのアプリケーションにとって EF6 が最適な選択となっています。

**次のタイプのアプリケーションでは、EF Core を使用されることをお勧めします。**

* EF Core に未実装の機能を必要としないアプリケーション。 EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。

* ユニバーサル Windows プラットフォーム (UWP) アプリケーションや ASP.NET Core アプリケーションなど、.NET Core をターゲットとするアプリケーション。 EF6 では .NET Framework (.NET Framework 4.5 など) が必要なため、これらのアプリケーションは EF6 を使用できません。

## <a name="guidance-for-existing-ef6-applications"></a>既存の EF6 アプリケーションに関するガイダンス

EF Core には根本的な変更が加えられているため、変更するやむを得ない理由がない限り、EF6 アプリケーションの EF Core への移行はお勧めできません。 新機能を利用するために EF Core に移行する場合には、開始する前に制限事項を確認してください。 EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。

**EF6 から EF Core への移行は、アップグレードではなく移植と考える必要があります。** 詳細については、「[Porting from EF6 to EF Core (EF6 から EF Core への移植)](porting/index.md)」をご覧ください。
