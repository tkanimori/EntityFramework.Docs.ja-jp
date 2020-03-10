---
title: 接続解除エンティティの使用 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 12138003-a373-4817-b1b7-724130202f5f
ms.openlocfilehash: f1ce44e7b00ec4c60a81ed850ce5c9d866495e1b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413367"
---
# <a name="working-with-disconnected-entities"></a>接続解除エンティティの使用
Entity Framework ベースのアプリケーションでは、追跡しているエンティティに適用された変更はコンテキスト クラスが検出します。 SaveChanges メソッドを呼び出すと、コンテキストによって追跡された変更がデータベースに永続化されます。 n 層アプリケーションを使用する場合、エンティティ オブジェクトはコンテキストから切り離されるときに通常変更され、変更を追跡する方法およびそれらの変更をコンテキストにレポートする方法を決定する必要があります。 このトピックでは、Entity Framework で接続解除エンティティを使用するときに使用できるさまざまなオプションについて説明します。   

## <a name="web-service-frameworks"></a>Web サービス フレームワーク

Web サービス テクノロジでは、個々の接続解除オブジェクトの変更の永続化に使用できるパターンを通常サポートしています。 たとえば、ASP.NET Web API では、データベース上のオブジェクトへの変更を永続化できる EF に対する呼び出しを含めることができるコントローラー アクションをコーディングできます。 実際、Visual Studio の Web API ツールでは、Entity Framework 6 モデルから Web API コントローラーを容易にスキャフォールディングできます。 詳細については、「[Using Web API with Entity Framework 6](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/)」 (Entity Framework 6 での Web API の使用) を参照してください。   

今までには、[WCF Data Services](https://docs.microsoft.com/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) や [RIA Services](https://docs.microsoft.com/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91)) など、Entity Framework に統合できる Web サービス テクノロジが他にもいくつかありました。

## <a name="low-level-ef-apis"></a>下位レベルの EF の API

既存の n 層ソリューションを使用しない場合や、Web API サービス内のコントローラー アクション内で起こることをカスタマイズしたい場合のために、Entity Framework には、接続解除された層に対して変更を適用する API が用意されています。 詳細については、[Add、Attach およびエンティティの状態](~/ef6/saving/change-tracking/entity-state.md)に関する説明を参照してください。  

## <a name="self-tracking-entities"></a>自己追跡エンティティ  

EF コンテキストから接続解除されている場合に、エンティティの任意のグラフで変更を追跡することは難しいです。 これを解決する 1 つの試みとして、Self-Tracking Entities ジェネレーション テンプレートがありました。 このテンプレートは、接続解除された層に対して行われた変更を、エンティティ自体の状態として追跡するロジックを含むエンティティ クラスを生成します。 それらの変更をコンテキストに適用する、拡張メソッドのセットも生成されています。

このテンプレートは、EF Designer を使用して作成されたモデルで使用できますが、Code First のモデルでは使用できません。 詳細については、「[自己追跡エンティティ](self-tracking-entities/index.md)」を参照してください。  

> [!IMPORTANT]
> 自己追跡エンティティ テンプレートの使用は現在お勧めしていません。 既存のアプリケーションをサポートするためにのみ引き続き使用できます。 アプリケーションで、エンティティの切断されたグラフを操作する必要がある場合は、代替の方法を検討してください。たとえば、コミュニティによってより積極的に開発された自己追跡エンティティに似たテクノロジである[追跡可能なエンティティ](https://trackableentities.github.io/)を使用するか、または、低レベルの変更追跡 API を使用してカスタム コードを記述してください。
