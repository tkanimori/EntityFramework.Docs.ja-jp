---
title: 空間型のプロバイダーサポート-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 863f1b4551bd62160915eba90fee7ba6c49c169c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413895"
---
# <a name="provider-support-for-spatial-types"></a>空間型のプロバイダーサポート
Entity Framework は、DbGeography クラスまたは Dbgeography クラスを使用した空間データの操作をサポートします。 これらのクラスは、Entity Framework プロバイダーによって提供されるデータベース固有の機能に依存しています。 すべてのプロバイダーが空間データをサポートするわけではありません。また、空間型アセンブリのインストールなどの追加の前提条件を持つプロバイダーもあります。 サポートされている空間型のプロバイダーの詳細については、以下を参照してください。  

アプリケーションでの空間型の使用方法に関する追加情報については、次の2つのチュートリアルを参照してください。1つは Code First 用、もう1つは Database First または Model First です。  

- [Code First の空間データ型](~/ef6/modeling/code-first/data-types/spatial.md)  
- [EF デザイナーの空間データ型](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a>空間型をサポートする EF のリリース  

空間型のサポートは、EF5 で導入されました。 ただし、EF5 空間型は、アプリケーションが .NET 4.5 を対象として実行されている場合にのみサポートされます。  

EF6 空間型以降では、.NET 4 と .NET 4.5 の両方を対象とするアプリケーションでサポートされています。  

## <a name="ef-providers-that-support-spatial-types"></a>空間型をサポートする EF プロバイダー  

### <a name="ef5"></a>EF5  

空間型をサポートしていることを認識している EF5 の Entity Framework プロバイダーは次のとおりです。  

- Microsoft SQL Server プロバイダー  
    - このプロバイダーは、EF5 の一部として出荷されています。  
    - このプロバイダーは、インストールする必要があるその他の下位レベルのライブラリに依存しています。詳細については、以下を参照してください。  
- [Devart dotConnect for Oracle](https://www.devart.com/dotconnect/oracle/)  
    - これは Devart のサードパーティプロバイダーです。  

空間型をサポートする EF5 プロバイダーがわかっている場合は、連絡先を取得してください。この一覧に追加することをお勧めします。  

### <a name="ef6"></a>EF6  

空間型をサポートしていることを認識している EF6 の Entity Framework プロバイダーは次のとおりです。  

- Microsoft SQL Server プロバイダー  
    - このプロバイダーは、EF6 の一部として出荷されています。  
    - このプロバイダーは、インストールする必要があるその他の下位レベルのライブラリに依存しています。詳細については、以下を参照してください。  
- [Devart dotConnect for Oracle](https://www.devart.com/dotconnect/oracle/)  
    - これは Devart のサードパーティプロバイダーです。  

空間型をサポートする EF6 プロバイダーがわかっている場合は、連絡先を取得してください。この一覧に追加することをお勧めします。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>Microsoft SQL Server を使用した空間型の前提条件  

SQL Server 空間サポートは、下位レベルの SQL Server 固有の型 SqlGeography および Sqlgeography に依存します。 これらの型は、Microsoft の SqlServer. .dll アセンブリに存在します。このアセンブリは、EF の一部として、または .NET Framework の一部として出荷されることはありません。  

Visual Studio をインストールすると、多くの場合、SQL Server のバージョンもインストールされます。これには、Microsoft の SqlServer. .dll のインストールが含まれます。  

空間型を使用するコンピューターに SQL Server がインストールされていない場合、または空間型が SQL Server のインストールから除外されている場合は、手動でインストールする必要があります。 これらの型は、Microsoft SQL Server Feature Pack の一部である `SQLSysClrTypes.msi`を使用してインストールできます。 空間の種類は SQL Server バージョンによって異なります。そのため、Microsoft ダウンロードセンターで["SQL Server Feature Pack" を検索](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack)し、使用する SQL Server のバージョンに対応するオプションを選択してダウンロードすることをお勧めします。
