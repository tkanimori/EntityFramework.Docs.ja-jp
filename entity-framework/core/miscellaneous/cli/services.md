---
title: デザイン時サービス-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655833"
---
# <a name="design-time-services"></a>デザイン時サービス

ツールで使用される一部のサービスは、デザイン時にのみ使用されます。 これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。 これらのサービスのいずれかをオーバーライドするには (たとえば、移行ファイルを生成するサービス)、スタートアッププロジェクトに `IDesignTimeServices` の実装を追加します。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
