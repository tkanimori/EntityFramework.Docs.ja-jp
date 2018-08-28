---
title: 新機能 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 9eb6a916d36ed41fcaea74564395695c048ab0f5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996336"
---
# <a name="whats-new-in-ef6"></a>EF6 の新機能

最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。
ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。
特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。

このページでは、新しい各リリースに含まれる機能を説明します。

## <a name="recent-releases"></a>最近のリリース

### <a name="ef-tools-update-in-visual-studio-2017-157"></a>Visual Studio 2017 15.7 の EF ツールの更新プログラム

2018 年 5 月に、Visual Studio 2017 15.7 の一部として EF ツールの更新バージョンをリリースしました。
これには、次のような一般的な問題点の機能強化が含まれています。

- ユーザー インターフェイス アクセシビリティに関するいくつかのバグの修正プログラム
- 既存のデータベースからモデルを生成するときの SQL Server のパフォーマンス低下の回避策 [#4](https://github.com/aspnet/entityframework6/issues/4)
- SQL Server での大規模モデルに対するモデル更新のサポート [#185](https://github.com/aspnet/EntityFramework6/issues/185)

この EF ツールの新しいバージョンの別の改善点として、新しいプロジェクトでのモデル作成時に EF 6.2 ランタイムがインストールされます。 以前のバージョンの Visual Studio では、NuGet パッケージの対応するバージョンをインストールすることで、EF 6.2 ランタイム (と過去のすべてのバージョンの EF) を使用することができます。

### <a name="ef-62-runtime"></a>EF 6.2 ランタイム

EF 6.2 ランタイムは、NuGet で 2017 年 10 月にリリースされました。
オープン ソースの共同作成者のコミュニティによる多大な尽力のおかげで、EF 6.2 には多数の[バグ修正](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug)と[製品の拡張機能](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20)が含まれています。

EF 6.2 ランタイムに影響を与える、特に重要な変更の簡単な一覧を次に示します。

- 永続的キャッシュから完了済みコードの最初のモデルを読み込むことで、起動時間が短縮される [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- インデックスを定義する fluent API [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- SQL で LIKE に変換される LINQ クエリの記述を有効にするための DbFunctions.Like() [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe で -script オプションがサポート [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 で SQL Server のシーケンスで生成されたキーの値が使用可能に [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- SQL Azure の実行方法に関する一時的なエラーのリストの更新 [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- バグ: クエリまたは SQL コマンドの再試行が "The SqlParameter is already contained by another SqlParameterCollection" (この SqlParameter は別の SqlParameterCollection に既に含まれています) で失敗する [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- バグ: DbQuery.ToString() の評価がデバッガーで頻繁にタイムアウトになる [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="future-releases"></a>リリースの予定

今後のバージョンの EF6 については、[ロードマップ](roadmap.md)を参照してください。

## <a name="past-releases"></a>以前のリリース

[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。
