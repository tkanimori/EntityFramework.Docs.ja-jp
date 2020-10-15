---
title: 関数のマッピング-Azure Cosmos DB Provider-EF Core
description: Azure Cosmos DB EF Core プロバイダーの関数マッピング
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066519"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Azure Cosmos DB EF Core プロバイダーの関数マッピング

このページでは、Azure Cosmos DB プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。

.NET                          | SQL                              | 追加されたバージョン:
----------------------------- | -------------------------------- | --------
表す.Contains (item)     | @item から @collection
stringValue。 Contains (値)   | CONTAINS ( @stringValue , @value )   | EF Core 5.0
EndsWith (値)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
FirstOrDefault ()  | LEFT ( @stringValue , 1)            | EF Core 5.0
LastOrDefault ()   | RIGHT ( @stringValue , 1)           | EF Core 5.0
StartsWith (値) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
