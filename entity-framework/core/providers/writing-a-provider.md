---
title: "プロバイダーの作成、データベースの EF コア"
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d6d3748a9097b3b8eeee2a8a516c53f3b2afa58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="writing-a-database-provider"></a>データベース プロバイダーの作成

Entity Framework のコア データベース プロバイダーの作成方法の詳細については、次を参照してください。 [EF コア プロバイダーを記述するように](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)によって[Arthur ヴィッカース](https://github.com/ajcvickers)です。

コード ベースを EF Core はオープン ソースであるため、参照として使用できるいくつかのデータベース プロバイダーを含んでいます。 https://github.com/aspnet/EntityFramework でソース コードを検索することができます。

## <a name="the-providers-beware-label"></a>プロバイダー ラベルに注意してください

プロバイダーでの作業を開始すると、監視、 [ `providers-beware` ](https://github.com/aspnet/EntityFramework/labels/providers-beware) GitHub の問題やプル要求に対するラベルです。 プロバイダーの作成者に影響を与える可能性の変更を特定するのにこのラベルを使用します。

## <a name="suggested-naming-of-third-party-providers"></a>サード パーティ プロバイダーの名前付けを推奨

NuGet パッケージの次の名前付けを使用することをお勧めします。 これは、同じ EF コア チームによって配信されるパッケージの名前です。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例:
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
