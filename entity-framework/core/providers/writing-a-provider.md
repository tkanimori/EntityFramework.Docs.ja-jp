---
title: データベース プロバイダー - EF Core の書き込み
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: c7130b0d104cd26584d298da98eb3e7080ee7f3c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993667"
---
# <a name="writing-a-database-provider"></a>データベース プロバイダーの作成

Entity Framework Core データベース プロバイダーの作成方法の詳細については、[、EF Core プロバイダーを記述するように](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)によって[Arthur Vickers](https://github.com/ajcvickers)を参照してください。

> [!NOTE]
> ブログの投稿が EF Core 1.1 以降に更新されていないと、その後大幅な変更も加えられて[問題 681](https://github.com/aspnet/EntityFramework.Docs/issues/681)がこのドキュメントに対する更新プログラムを追跡します。

EF Core のコードベースはオープン ソースであるため、参照として使用できるいくつかのデータベース プロバイダーを含んでいます。 ソース コードを検索することができます https://github.com/aspnet/EntityFrameworkCore します。 サード パーティ プロバイダーの一般的に使用される場合など、コードを検索する便利な場合もあります[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)、および[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)します。 具体的には、これらのプロジェクトは、セットアップから拡張して、NuGet で公開される機能テストを実行します。 この種のセットアップは強くお勧めします。

## <a name="keeping-up-to-date-with-provider-changes"></a>プロバイダーの変更で最新に保つ

作成した 2.1 のリリース後、以降の作業を[の変更ログ](provider-log.md)プロバイダ コードに対応する変更する必要があります。 これは、目的は、新しいバージョンの EF Core を使用する既存のプロバイダーを更新するときにします。

使用して 2.1 では、前に、 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) GitHub の懸案事項と同様の目的のプル要求のラベル。 ここでの問題をこれらのラベルを使用しているかを示す特定のリリースでは、どの作業項目は、プロバイダーで実行する作業を必要がありますを continiue します。 A`providers-beware`ラベル、通常、作業項目の実装が、プロバイダーを分割ことを意味中に、`providers-fyi`プロバイダーは切断されますが、コードは、変更するなどの新機能を有効にする必要があります、通常ラベルを意味します。

## <a name="suggested-naming-of-third-party-providers"></a>サード パーティ プロバイダーの名前付けの推奨

NuGet パッケージの次の名前付けを使用することをお勧めします。 これは、EF Core チームによって提供されるパッケージの名前と一致します。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例えば:
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
