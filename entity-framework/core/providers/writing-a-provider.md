---
title: データベースプロバイダーの作成-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 2d9e4a6cdfda80d7dfcfb6e7bf0480eb49f8e057
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414807"
---
# <a name="writing-a-database-provider"></a>データベース プロバイダーを記述する

Entity Framework Core データベースプロバイダーの作成の詳細については、「 [Arthur ヴィッカース](https://github.com/ajcvickers)」を参照して[EF Core プロバイダーを作成](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)することをお勧めします。

> [!NOTE]
> これらの投稿は EF Core 1.1 以降に更新されていません。この[問題が 681](https://github.com/dotnet/EntityFramework.Docs/issues/681)で発生したため、このドキュメントの更新が追跡されています。

EF Core codebase はオープンソースであり、参照として使用できるデータベースプロバイダーがいくつか含まれています。 ソースコードについては、<https://github.com/aspnet/EntityFrameworkCore>を参照してください。 また、 [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)、 [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)など、一般的に使用されるサードパーティプロバイダーのコードを確認すると役立つ場合もあります。 特に、これらのプロジェクトは、NuGet で公開する機能テストをから拡張して実行するように設定されています。 この種のセットアップは強くお勧めします。

## <a name="keeping-up-to-date-with-provider-changes"></a>プロバイダーの変更を最新の状態に保つ

2\.1 リリース後の作業では、プロバイダーコードの対応する変更が必要になる可能性[のある変更のログ](provider-log.md)を作成しました。 これは、既存のプロバイダーを更新して新しいバージョンの EF Core で動作する場合に役立ちます。

2\.1 より前では、GitHub の問題とプル要求について、 [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)ラベルを使用して、同様の目的をしていました。 これらの問題に対してこれらの continiue を使用して、特定のリリースのどの作業項目についても、プロバイダーでの作業が必要になる可能性があることを示すことができます。 `providers-beware` ラベルは、通常、作業項目の実装によってプロバイダーが壊れる可能性があることを意味します。一方、`providers-fyi` ラベルは、プロバイダーが破損しないことを意味しますが、新しい機能を有効にするなど、コードを変更する必要がある場合があります。

## <a name="suggested-naming-of-third-party-providers"></a>サードパーティプロバイダーの推奨される名前付け

NuGet パッケージには、次の名前付けを使用することをお勧めします。 これは、EF Core チームによって配信されるパッケージの名前と一致します。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

次に例を示します。

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
