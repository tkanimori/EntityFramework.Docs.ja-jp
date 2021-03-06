---
title: パフォーマンスの概要 - EF Core
description: Entity Framework Core を効率的に使用するためのパフォーマンス ガイド
author: roji
ms.date: 12/1/2020
uid: core/miscellaneous/performance/index
ms.openlocfilehash: 14400d81ea3c93e2ebf40e8e585a457abf31478f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657686"
---
# <a name="introduction-to-performance"></a>パフォーマンスの概要

データベースのパフォーマンスは、データベース、ネットワーク、データベース ドライバー、およびデータ アクセス層 (EF Core など) といったコンポーネントのスタック全体にまたがる、膨大で複雑なトピックです。 上位層および O/RM (EF Core など) により、アプリケーションの開発を大幅に簡素化し、保守容易性を向上させることができますが、透過性が損なわれて、パフォーマンスに関する重大な内部的詳細 (実行中の SQL など) が隠されることがあります。 このセクションでは、EF Core で優れたパフォーマンスを実現する方法の概要を示し、アプリケーションのパフォーマンスを低下させるおそれのある、よくある落とし穴を回避する方法について説明します。

## <a name="identify-bottlenecks-and-measure-measure-measure"></a>ボトルネックを特定し、徹底的に測定する

パフォーマンスについて常に言えることですが、問題を示すデータなしに性急に最適化を行わないようにすることが重要です。偉大な Donald Knuth 氏の言葉にあるとおり、"早まった最適化は諸悪の根源" です。 [パフォーマンス診断](xref:core/performance/performance-diagnosis)に関するセクションでは、アプリケーションでデータベース ロジックに時間が費やされている場所を把握するためのさまざまな方法と、問題のある特定の領域を特定する方法について説明します。 低速のクエリが特定されたら、解決策を検討できます。データベースにインデックスがないのでしょうか? また、他のクエリ パターンを試してみる必要はあるでしょうか?

常にコードおよび可能な選択肢を自分でベンチマークします。パフォーマンス診断のセクションには、BenchmarkDotNet を使用したサンプル ベンチマークが含まれています。これは、独自のベンチマークのテンプレートとして使用できます。 一般的なパブリック ベンチマークが特定のユースケースにそのまま適用されるとは考えないでください。テーブル内のデータベースの待機時間、クエリの複雑さ、実際のデータ量など、さまざまな要因が、どのソリューションが最適かということに大きな影響を与える場合があります。 たとえば、多くのパブリック ベンチマークは、データベースへの待機時間がほぼゼロの最適なネットワーク条件で、データベース側の処理 (またはディスク I/O) を必要としない非常に軽いクエリで実行されます。 これらは、さまざまなデータ アクセス層のランタイム オーバーヘッドを比較するのに役立ちますが、通常、これらの違いは実際のアプリケーションではごくわずかです。実際のアプリケーションでは、データベースによって実際の作業が実行され、データベースへの待機時間が重要なパフォーマンス要因となります。

## <a name="aspects-of-data-access-performance"></a>データ アクセス パフォーマンスの側面

全体的なデータ アクセス パフォーマンスは、次の広範なカテゴリに分類できます。

* **純粋なデータベース パフォーマンス**。 リレーショナル データベースでは、EF によって、アプリケーションの LINQ クエリが、データベースによって実行される SQL ステートメントに変換されます。これらの SQL ステートメント自体は、おおむね効率的に実行できます。 適切な場所に適切なインデックスを使用すると、SQL のパフォーマンスを向上させることができます。また、LINQ クエリを再記述すると、EF によってより適切な SQL クエリが生成される可能性があります。
* **ネットワーク データ転送**。 ネットワーク システムと同様に、ネットワーク上でやり取りされるデータの量を制限することが重要です。 これにより、実際に必要なデータのみを送信および読み込みできるようになります。また、関連エンティティを読み込むときに、いわゆる "デカルト爆発" 効果を回避できます。
* **ネットワーク ラウンドトリップ**。 データベース内でクエリを実行するための時間は、アプリケーションとデータベースの間をパケットが行き来する時間のために短縮される可能性があるため、データのやり取りの量を超えてネットワーク ラウンドトリップが行われます。 ラウンドトリップのオーバーヘッドは環境に大きく依存します。データベース サーバーが遠くなるほど、待機時間が長くなり、各ラウンドトリップのコストが増加します。 クラウドの登場により、データベースからアプリケーションまでが遠くなり、実行するラウンドトリップが多すぎる "chatty" (頻繁な、饒舌な) アプリケーションのパフォーマンスが低下します。 そのため、アプリケーションからデータベースに接続されるタイミング、実行されるラウンドトリップの回数、およびその数を最小限に抑えることができるかどうかを、正確に把握することが重要です。
* **EF ランタイム オーバーヘッド**。 最後に、EF 自体により、データベース操作にいくらかのランタイム オーバーヘッドが追加されます。たとえば、EF では LINQ から SQL にクエリをコンパイルする必要があります (通常は 1 回だけ実行します)。また、変更の追跡によって、いくらかのオーバーヘッドが追加されます (ただし、無効にすることもできます)。実際には、データベースでのクエリの実行時間とネットワーク待機時間が合計時間の大部分を占めるため、ほとんどの場合、実際のアプリケーションの EF オーバーヘッドはごくわずかになる可能性があります。ただし、オプションの内容と、いくつかの落とし穴を回避する方法を理解しておくことが重要です。

## <a name="know-whats-happening-under-the-hood"></a>内部で起こっている内容を把握する

EF を使用すると、開発者は SQL を生成し、結果を具体化し、その他のタスクを実行することで、ビジネス ロジックに専念できます。 また、レイヤーや抽象化と同様に、内部で起こっている内容 (実際に実行されている SQL クエリなど) が隠される傾向があります。 パフォーマンスは、すべてのアプリケーションの重要な側面であるとは限りませんが、それに該当するアプリケーションでは、EF によって実行されている内容を開発者が把握しておくことが重要です (出力方向の SQL クエリを調査する、ラウンドトリップを追跡して N+1 の問題が発生していないことを確認する、など)。

## <a name="cache-outside-the-database"></a>データベースの外部でのキャッシュ

最後に、データベースを操作する最も効率的な方法は、データベースをできる限り操作しないことです。 つまり、データベース アクセスがアプリケーションのパフォーマンスのボトルネックである場合は、要求を最小限に抑えるために、データベースの外部で特定の結果をキャッシュすると有用な可能性があります。 キャッシュは複雑になりますが、これは拡張性の高いアプリケーションでは特に重要です。アプリケーション層は、負荷の増加に対応するためにサーバーを追加することで簡単に拡張できますが、データベース層のスケーリングは、通常、はるかに複雑になります。
