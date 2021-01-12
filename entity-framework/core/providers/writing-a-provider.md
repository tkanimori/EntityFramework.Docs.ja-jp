---
title: データベースプロバイダーの作成-EF Core
description: 新しい Entity Framework Core プロバイダーの作成に関する情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: e66c5b94d826e35bb5148d57897a1081de4e9736
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128421"
---
# <a name="writing-a-database-provider"></a>データベース プロバイダーを記述する

Entity Framework Core データベースプロバイダーの作成の詳細については、「 [Arthur ヴィッカース](https://github.com/ajcvickers)」を参照して[EF Core プロバイダーを作成](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)することをお勧めします。

> [!NOTE]
> これらの投稿は EF Core 1.1 以降に更新されていないため、その時点以降に大幅な変更が加えられています。
[問題 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) は、このドキュメントの更新を追跡しています。

EF Core codebase はオープンソースであり、参照として使用できるデータベースプロバイダーがいくつか含まれています。 ソースコードについては、「」を参照して <https://github.com/dotnet/efcore> ください。 また、 [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)、 [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)など、一般的に使用されるサードパーティプロバイダーのコードを確認すると役立つ場合もあります。 特に、これらのプロジェクトは、NuGet で公開する機能テストから拡張して実行するように設定されています。 この種のセットアップは強くお勧めします。

## <a name="keeping-up-to-date-with-provider-changes"></a>プロバイダーの変更を最新の状態に保つ

2.1 リリース後の作業では、プロバイダーコードの対応する変更が必要になる可能性 [のある変更のログ](xref:core/providers/provider-log) を作成しました。 これは、既存のプロバイダーを更新して新しいバージョンの EF Core で動作する場合に役立ちます。

2.1 より前では、 [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) GitHub の [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) 問題とプル要求について、とのラベルを同様の目的で使用していました。 これらの問題に対してこれらの continiue を使用して、特定のリリースのどの作業項目についても、プロバイダーでの作業が必要になる可能性があることを示すことができます。 ラベルは、 `providers-beware` 通常、作業項目の実装によってプロバイダーが壊れる可能性があることを意味しますが、 `providers-fyi` ラベルは通常、プロバイダーが破損しないことを意味しますが、新しい機能を有効にするなど、コードを変更する必要がある場合があります。

## <a name="suggested-naming-of-third-party-providers"></a>サードパーティプロバイダーの推奨される名前付け

NuGet パッケージには、次の名前付けを使用することをお勧めします。 これは、EF Core チームによって配信されるパッケージの名前と一致します。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

次に例を示します。

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
