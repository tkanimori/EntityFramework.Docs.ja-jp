---
title: デザイン時サービス-EF Core
description: Entity Framework Core デザイン時サービスに関する情報
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617852"
---
# <a name="design-time-services"></a>デザイン時サービス

ツールで使用される一部のサービスは、デザイン時にのみ使用されます。 これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。 これらのサービスの1つ (たとえば、移行ファイルを生成するサービス) をオーバーライドするには、の実装を `IDesignTimeServices` スタートアッププロジェクトに追加します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
