---
title: EF Core と EF6 を比較する
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4609ecbc9e24d8a359694d256523c64141b5ff62
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2018
ms.locfileid: "30002757"
---
# <a name="compare-ef-core--ef6"></a>EF Core と EF6 を比較する

Entity Framework には 2 つの異なるバージョンがあります。Entity Framework Core と Entity Framework 6 です。

## <a name="entity-framework-6"></a>Entity Framework 6

Entity Framework 6 (EF6) は試験の結果、信頼性が立証されたデータ アクセス テクノロジです。何年にもわたり機能が蓄積され、安定しています。 2008 年に初めて、.NET Framework 3.5 SP1 と Visual Studio 2008 SP1 の一部としてリリースされました。 EF4.1 リリース以降、[EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)として出荷されています。これは NuGet.org で現在最も人気のあるパッケージの 1 つです。

EF6 は製品として今後もサポートされます。しばらくの間、バグ修正や細かな改善も提供されます。

## <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) は Entity Framework の軽量版であり、拡張性に優れ、プラットフォームに依存しません。 EF Core は EF6 と比べ、たくさんの機能改善と新機能が導入されています。 一方で、EF Core は新しいコード ベースであり、EF6 ほど成熟していません。

開発者にとっては EF Core の使用感は EF6 と変わらず、ほとんどの上位 API は同じまま残っています。そのため、EF6 を使用した者にとっては、EF Core は非常になじみのあるフレームワークです。 一方で、EF Core はまったく新しいコア コンポーネント セットを土台にしています。 つまり、EF Core は EF6 からすべての機能を自動的に継承しているわけではありません。 一部の機能は今後のリリースで導入されますが、一般的にはあまり使用されない機能は EF Core には実装されません。

拡張性に優れ、軽量の新しいコアによって、EF6 では実装されない一部の機能も EF Core に追加できました (代替キー、バッチ更新、LINQ クエリのクライアント/データベース混合評価など)。
