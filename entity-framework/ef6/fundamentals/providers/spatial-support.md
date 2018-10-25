---
title: 空間型 - EF6 のプロバイダーのサポート
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 9c00e82c663daec219fe649a8d889afcc81564f7
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022276"
---
# <a name="provider-support-for-spatial-types"></a>空間型のプロバイダー サポート
Entity Framework では、DbGeography または DbGeometry クラスを使用して空間データの操作をサポートしています。 これらのクラスは、Entity Framework プロバイダーによって提供されるデータベースに固有の機能に依存します。 空間データをサポートしていないすべてのプロバイダーと、空間型のアセンブリのインストールなどの追加の前提条件はいる場合があります。 空間型のプロバイダー サポートの詳細については、次に示します。  

アプリケーションで空間型を使用する方法の詳細については、Database First または Model First をその他の Code First での 1 つ、2 つのチュートリアルではあります。  

- [空間データ型は、最初のコード](~/ef6/modeling/code-first/data-types/spatial.md)  
- [EF デザイナーで空間データ型](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a>空間型をサポートする EF のリリース  

空間型のサポートは、EF5 で導入されました。 ただし、EF5 で空間型は場合にのみアプリケーションを対象とし、.NET 4.5 で実行されます。  

EF6 の空間型は .NET 4 および .NET 4.5 の両方を対象とするアプリケーションのサポートを開始しています。  

## <a name="ef-providers-that-support-spatial-types"></a>空間型をサポートする EF プロバイダー  

### <a name="ef5"></a>EF5  

空間型のサポートがの認識は EF5 の Entity Framework プロバイダー:  

- Microsoft SQL Server プロバイダー  
    - このプロバイダーは EF5 の一部として出荷されます。  
    - このプロバイダーによって異なりますいくつか追加の低レベル ライブラリをインストールする必要があります-詳細については、以下を参照してください。  
- [Devart dotConnect for Oracle](http://www.devart.com/dotconnect/oracle/)  
    - Devart からサード パーティ プロバイダーです。  

わかっている場合してください、空間型をサポートする EF5 プロバイダーの連絡先を取得し、この一覧に追加することをお知らせいたします。  

### <a name="ef6"></a>EF6  

空間型のサポートがの認識が EF6 の Entity Framework プロバイダー:  

- Microsoft SQL Server プロバイダー  
    - このプロバイダーは、EF6 の一部として出荷されます。  
    - このプロバイダーによって異なりますいくつか追加の低レベル ライブラリをインストールする必要があります-詳細については、以下を参照してください。  
- [Devart dotConnect for Oracle](http://www.devart.com/dotconnect/oracle/)  
    - Devart からサード パーティ プロバイダーです。  

わかっている場合してください、空間型をサポートする EF6 プロバイダーの連絡先を取得し、この一覧に追加することをお知らせいたします。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>Microsoft SQL Server での空間型の前提条件  

SQL Server 空間のサポートは、低レベルの SQL Server に固有の型を SqlGeography および SqlGeometry に依存します。 アセンブリ Microsoft.SqlServer.Types.dll でこれらの型が存在し、EF の一部として、または .NET Framework の一部として、このアセンブリが付属していません。  

Visual Studio がインストールされている場合、SQL Server のバージョンも多くの場合、インストールされ、これは、Microsoft.SqlServer.Types.dll のインストールが含まれます。  

空間型を使用するコンピューターの SQL Server がインストールされていない場合、または空間型は、SQL Server のインストールから除外された場合は、それらを手動でインストールする必要があります。 使用して、種類をインストールすることができます`SQLSysClrTypes.msi`、Microsoft SQL Server Feature Pack の一部です。 空間型は、バージョン固有の SQL Server をお勧め["SQL Server Feature Pack"を検索](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack)Microsoft ダウンロード センターを選択し、使用する SQL Server のバージョンに対応するオプションをダウンロードします。
