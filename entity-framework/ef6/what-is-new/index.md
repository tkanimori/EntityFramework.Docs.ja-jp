---
title: 新機能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149117"
---
# <a name="whats-new-in-ef6"></a>EF6 の新機能

最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。
ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。
特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。

## <a name="ef-630"></a>EF 6.3.0

EF 6.3.0 ランタイムは、NuGet で 2019 年 9 月にリリースされました。 このリリースの主な目的は、EF 6 を使用する既存のアプリケーションを .NET Core 3.0 に円滑に移行することです。 コミュニティによって、いくつかのバグ修正と機能強化も提供されています。 詳細については、各 [6.3.0](https://github.com/aspnet/EntityFramework6/milestones?state=closed) マイルストーンで解決されたイシューを参照してください。 次に、重要なものをいくつか紹介します。

- .NET Core 3.0 のサポート
  - EntityFramework パッケージでは、.NET Framework 4.x に加え .NET Standard 2.1 をターゲットとするようになりました。
  - 移行コマンドは、アウトプロセスの実行と SDK 形式のプロジェクトで動作するよう、書き替えられています。
- SQL Server HierarchyId のサポート
- Roslyn と NuGet PackageReference との互換性の向上
- アセンブリからの移行を有効化、追加、スクリプト作成、および適用する ef6.exe を追加しました。 これは、migrate.exe の代わりです

## <a name="past-releases"></a>以前のリリース

[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。
