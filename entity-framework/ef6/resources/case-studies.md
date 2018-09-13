---
title: Entity Framework の EF6 に関するケース スタディ
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: d7982a3f89ac1e57b48039d828f287adf6dc5068
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490884"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Entity Framework 用のマイクロソフト ケース スタディ
このページで、ケース スタディは、Entity Framework が採用されてきましたが、いくつかの実際の運用プロジェクトを選択します。
> [!NOTE]
> これらのケース スタディの詳細なバージョンでは、Microsoft web サイトで使用できなくします。 そのため、リンクが削除されました。

## <a name="epicor"></a>Epicor
Epicor は、150 か国以上の企業のエンタープライズ リソース プランニング (ERP) ソリューションを開発している (400 を超える開発者) で大規模なグローバル ソフトウェア会社です。
同社の主力製品、Epicor 9 は、上、サービス指向アーキテクチャ (SOA) .NET Framework を使用してに基づいています。
言語統合クエリ (LINQ)、および、バックエンドの SQL Server の負荷を軽減する必要があるものサポートを提供する多数の顧客の要求に直面して、チームは、Visual Studio 2010 および .NET Framework 4.0 にアップグレードする決定します。
Entity Framework 4.0 を使用することができましたをこれらの目標を達成し、開発と保守を大幅に簡略化します。
具体的には、Entity Framework の豊富な T4 のサポートは、生成されたコードの完全に制御し、コンパイル済みクエリやキャッシュなどのパフォーマンスを削減する機能で自動的にビルドすることを許可します。

> "既存のコードで最近のいくつかのパフォーマンス テストを実施しましたおよび 90% の SQL Server への要求を削減することができました。
あるため、ADO.NET Entity Framework 4。" – Erik Johnson、バイス プレジデント、製品の調査  

## <a name="veracity-solutions"></a>信憑性ソリューション
強力で使いやすい Rich Internet Application が Silverlight 4 に基づくように再作成するのに Visual Studio 2010 を使用信憑性ソリューションに維持し、長期的に拡張が困難になることが、イベント計画ソフトウェア システムを取得します。
.NET RIA Services を使用して、それらがコードの重複を回避、層間での一般的な検証と認証ロジックに許可されている Entity Framework の上にサービス層を迅速に構築できました。  

> "販売された Entity Framework で初めて導入された、および Entity Framework 4 が良い実証済みのときにします。
ツールが改善され、概念モデル、ストレージ モデル、およびそれらのモデル間のマッピングを定義する .edmx ファイルを操作しやすくしています.Entity framework では、そのデータ アクセス層の 1 日での作業を表示、および移動してビルドのアウトします。
Entity Framework は、業界標準のデータ アクセス層です。わからない理由、だれでも使用しません。" – Joe McBride、上級開発者

## <a name="nec-display-solutions-of-america"></a>アメリカの NEC 表示ソリューション
NEC 広告とネットワークの所有者を利用し、独自の収益の増加するためのソリューションを場所ベースのデジタル広告の市場に参入したいです。
そのためには、従来の広告キャンペーンで必要な手動プロセスを自動化する web アプリケーションのペアを起動します。
ASP.NET、Silverlight 3、AJAX および WCF では、SQL Server 2008 との対話には、データ アクセス層で Entity Framework と共に使用して、サイトを構築しました。

> 「SQL server とリアルタイムで、ミッション クリティカルなアプリケーションの情報は常に使用できるようにするための信頼性情報を広告主とネットワークのサービスを提供する必要がありましたスループットを取得することも」-Mike Corcoran、担当部長 IT

## <a name="darwin-dimensions"></a>ダーウィン ディメンション
広範なマイクロソフト テクノロジを使用して、ダーウィンのチームに着手 Evolver - ゲーム、アニメーション、およびソーシャル ネットワー キングのページで使用するための魅力的かつリアルなアバターを作成するコンシューマーが使用できるオンライン アバター ポータルを作成します。
生産性の利点、Entity Framework と Windows Workflow Foundation (WF) および Windows Server AppFabric (拡張性の高いメモリ内アプリケーション キャッシュ) などのコンポーネントに取り込むと、チームが 35% で、驚くような製品を配信できません。開発時間です。
であっては、チーム メンバーは、毎週のリリースと、アジャイル開発プロセスの後に、チームを複数の国の間で分割します。

 > "私たちはテクノロジーのためのテクノロジを作成しないようにしてください。 スタートアップ、時間と費用を節約するテクノロジを利用する非常に重要です。
 .NET が高速でコスト効率の高い開発を選択します。" – Zachary Olsen、アーキテクト  

## <a name="silverware"></a>そろ
小規模および中規模のレストランのグループの販売時点管理 (POS) ソリューションの開発での経験を 15 年以上、そろの開発チーム設定引き付け、拡大するために多くのエンタープライズ レベルの機能で、製品を強化するためにレストラン チェーンです。
マイクロソフトの開発ツールの最新バージョンを使用して、それらが 4 回よりも高速の前に新しいソリューションをビルドできました。
主な新機能などの LINQ および Entity Framework およびレポート要件をデータ記憶域の Crystal Reports から SQL Server 2008 および SQL Server Reporting Services (SSRS) に移動しやすくなります。

> 「効果的なデータ管理そろ – の成功の鍵は、このため、SQL Reporting を採用することにしました。」 -Nicholas Romanidis、部長 IT ソフトウェア エンジニア リング/
