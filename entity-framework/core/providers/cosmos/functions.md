---
title: 関数のマッピング-Azure Cosmos DB Provider-EF Core
description: Azure Cosmos DB EF Core プロバイダーの関数マッピング
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543589"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Azure Cosmos DB EF Core プロバイダーの関数マッピング

このページでは、Azure Cosmos DB プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。

.NET                          | SQL                              | 追加されたバージョン:
----------------------------- | -------------------------------- | --------
表す.Contains (item)     | @item から @collection
EF.Functions。 Random ()         | RAND ()                           | EF Core 6.0
stringValue。 Contains (値)   | CONTAINS ( @stringValue , @value )   | EF Core 5.0
EndsWith (値)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
FirstOrDefault ()  | LEFT ( @stringValue , 1)            | EF Core 5.0
LastOrDefault ()   | RIGHT ( @stringValue , 1)           | EF Core 5.0
StartsWith (値) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
