---
title: Entity Framework のケーススタディ-EF6
description: Entity Framework 6 のケーススタディ
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: f6cabe34cfbdc5f15758678f458b2c0898198137
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620240"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Entity Framework のための Microsoft のケーススタディ
このページのケーススタディでは、Entity Framework を採用した実際の実稼働プロジェクトをいくつか取り上げています。
> [!NOTE]
> これらのケーススタディの詳細なバージョンは、Microsoft web サイトでは利用できなくなりました。 そのため、リンクは削除されています。

## <a name="epicor"></a>E絵文字または
E絵文字またはは、150を超える国で企業向けのエンタープライズリソースプランニング (ERP) ソリューションを開発する大規模なグローバルソフトウェア企業です (400 開発者を対象としています)。
これらの主力製品である E絵文字または9は、.NET Framework を使用するサービス指向アーキテクチャ (SOA) に基づいています。
統合言語クエリ (LINQ) をサポートする多くの顧客要求に直面しています。また、バックエンド SQL サーバーの負荷を軽減するために、チームは Visual Studio 2010 および .NET Framework 4.0 にアップグレードすることを決定しました。
Entity Framework 4.0 を使用して、これらの目標を達成し、開発と保守を大幅に簡素化することができました。
特に、Entity Framework の豊富な T4 サポートによって、生成されたコードを完全に制御し、コンパイル済みのクエリやキャッシュなどのパフォーマンスを節約する機能を自動的に構築することが可能になりました。

> "既存のコードを使用していくつかのパフォーマンステストを最近実行しましたが、要求を 90% SQL Server に減らすことができました。
これは、ADO.NET Entity Framework 4 が原因です。 " – Erik ジョンソン、副社長、製品研究  

## <a name="veracity-solutions"></a>信憑性ソリューション
信憑性ソリューションでは、長期にわたって保守および拡張することが困難なイベント計画ソフトウェアシステムを獲得しました。 Visual Studio 2010 を使用して、Silverlight 4 上に構築された強力で使いやすいリッチなインターネットアプリケーションとして再書き込みします。
.NET RIA Services を使用すると、コードの重複を回避し、階層全体で共通の検証と認証のロジックを使用できるようにする、Entity Framework 上にサービス層をすばやく構築できました。  

> "最初に導入されたときに Entity Framework で販売されており、Entity Framework 4 はさらに優れていることがわかっています。
ツールが改善され、概念モデル、ストレージモデル、およびこれらのモデル間のマッピングを定義する .edmx ファイルを簡単に操作できるようになりました。Entity Framework を使用すると、データアクセス層を1日で動作させることができます。
Entity Framework は事実上、データアクセス層です。だれかがそれを使用しない理由はわかりません。 " – Joe McBride、シニア開発者

## <a name="nec-display-solutions-of-america"></a>NEC の表示ソリューション (NEC)
NEC は、広告やネットワークの所有者にメリットをもたらすソリューションを使用して、デジタルプレースベースの広告を市場に投入することを望んでいます。
これを実現するために、従来の ad キャンペーンで必要とされる手動プロセスを自動化する web アプリケーションのペアを開始しました。
サイトは、ASP.NET、Silverlight 3、AJAX、WCF を使用して構築されており、データアクセス層の Entity Framework と SQL Server 2008 と通信します。

> 「SQL Server では、データをリアルタイムで提供するために必要なスループットを取得し、ミッションクリティカルなアプリケーションの情報が常に利用可能であることを保証するために、信頼性を確保することができました。

## <a name="darwin-dimensions"></a>ダーウィンディメンション
Microsoft のさまざまなテクノロジを使用して、ダーウィンのチームは Evolver-オンラインアバターポータルを作成しました。コンシューマーはこのポータルを使用して、ゲーム、アニメーション、ソーシャルネットワークの各ページで使用するための、魅力的でリアルなアバターを作成できます。
Entity Framework の生産性向上と、Windows Workflow Foundation (WF) や Windows Server AppFabric (拡張性の高いメモリ内アプリケーションキャッシュ) などのコンポーネントを利用することで、チームは35% の開発時間ですばらしい製品を提供できました。
チームメンバーが複数の国にわたって分割されていても、チームは週単位のリリースでアジャイル開発プロセスに従っています。

 > 「テクノロジを作成しようとしていません。 起動時には、時間と費用を節約するテクノロジを活用することが重要です。
 .NET は、高速で費用対効果の高い開発に適しています。」 – Zachary Olsen、アーキテクト  

## <a name="silverware"></a>Silverware
小規模および中規模のレストラングループのために、販売時点管理 (POS) ソリューションの開発には15年以上の経験がありますが、Silverware の開発チームは、より大きなレストランのチェーンを惹き付けるために、より多くのエンタープライズレベルの機能で製品を強化するように設定されています。
最新バージョンの Microsoft 開発ツールを使用すると、新しいソリューションを以前よりも4倍速くビルドできました。
LINQ や Entity Framework などの重要な新機能により、データストレージとレポートのニーズに応じて、Crystal Reports から SQL Server 2008 および SQL Server Reporting Services (SSRS) に移行しやすくなりました。

> "効果的なデータ管理は SilverWare の成功にとって重要なので、このようにして SQL Reporting を採用することにしました。" -IT/ソフトウェアエンジニアリングのディレクターである Nicholas Romanidis
