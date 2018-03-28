---
title: EF Core と EF6 - 自社に適しているのはどちらか
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: f0a632902384a65ea3cddf752ad262c7a2e89e2e
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2018
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a>EF Core と EF6 - 自社に適しているのはどちらか

次の情報は、Entity Framework Core と Entity Framework 6 のどちらを選ぶかを決める場合に役立ちます。

## <a name="guidance-for-new-applications"></a>新しいアプリケーションのガイダンス

EF Core のすべての機能を活用する必要があり、EF Core にまだ実装されていない任意の機能を自分のアプリケーションで必要としない場合は、新しいアプリケーションに EF Core を使用することを検討してください。

EF6 には .NET Framework 4.0 (または以降のバージョン) が必要であり、Windows 上でのみサポートされますが (つまり、.NET Core では実行されず、その他のオペレーティング システムではサポートされません)、これらの制約を許容でき、アプリケーションに EF6 では利用できない EF Core の新機能が必要なければ、EF6 を引き続き新しいアプリケーションに選択することができます。

EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。

## <a name="guidance-for-existing-ef6-applications"></a>既存の EF6 アプリケーションに関するガイダンス

EF Core には根本的な変更が加えられているため、変更するやむを得ない理由がない限り、EF6 アプリケーションの EF Core への移行はお勧めできません。 新機能を利用するために EF Core に移行する場合には、開始する前に制限事項を確認してください。 EF Core が自社のアプリケーションにとって適切な選択かどうかを確認するには、[機能の比較](features.md)に関する記事をご覧ください。

**EF6 から EF Core への移行は、アップグレードではなく移植と考える必要があります。** 詳細については、「[Porting from EF6 to EF Core (EF6 から EF Core への移植)](porting/index.md)」をご覧ください。
