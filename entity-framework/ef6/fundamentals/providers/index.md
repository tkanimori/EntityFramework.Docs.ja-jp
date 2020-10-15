---
title: Entity Framework プロバイダー - EF6
description: Entity Framework 6 の Entity Framework プロバイダー
author: ajcvickers
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: d53cc98471597e98d5f54024b87e1f238b31473b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065408"
---
# <a name="entity-framework-6-providers"></a>Entity Framework 6 プロバイダー
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

Entity Framework は現在、オープンソース ライセンスで開発されており、EF6 以上は、.NET Framework の一部として出荷されません。 これには多くの利点がありますが、EF のプロバイダーを EF6 アセンブリに対して再構築する必要もあります。 これは、EF5 以下用の EF プロバイダーは、再構築されるまで EF6 で動作しないことを意味します。

## <a name="which-providers-are-available-for-ef6"></a>EF6 ではどのプロバイダーを使用できますか

以下のプロバイダーは、EF6 用に再構築されていることを認識されています。

*   **Microsoft SQL Server プロバイダー**
    *   [Entity Framework オープン ソース コード ベース](https://github.com/aspnet/EntityFramework6)から構築されました。
    *   [EntityFramework NuGet パッケージ](https://nuget.org/packages/EntityFramework)の一部として出荷されます。
*   **Microsoft SQL Server Compact Edition プロバイダー**
    *   [Entity Framework オープン ソース コード ベース](https://github.com/aspnet/EntityFramework6)から構築されました。
    *   [EntityFramework.SqlServerCompact NuGet パッケージ](https://nuget.org/packages/EntityFramework.SqlServerCompact)に付属しています。
*   [**Devart dotConnect データ プロバイダー**](https://www.devart.com/dotconnect/)
    *   Oracle、MySQL、PostgreSQL、SQLite、Salesforce、DB2、SQL Server などのを含むさまざまなデータベース用のサードパーティ プロバイダーが [Devart](https://www.devart.com/) から提供されています。
*   [**CData Software プロバイダー**](https://www.cdata.com/ado/)
    *   Salesforce、Azure Table Storage、MySql、その他多くのさまざまなデータ ストア用のサードパーティ プロバイダーが [CData Software](https://www.cdata.com/ado/) から提供されています。
*   **Firebird プロバイダー**
    *   [NuGet パッケージ](https://www.nuget.org/packages/EntityFramework.Firebird/)として入手できます。
*   **Visual Fox Pro プロバイダー**
    *   [NuGet パッケージ](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)として入手できます。
*   **MySQL**
    *   [Entity Framework の MySQL コネクタ/NET](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql は [NuGet パッケージ](https://www.nuget.org/packages/EntityFramework6.Npgsql/)として入手できます。
*   **Oracle**
    *   ODP.NET は [NuGet パッケージ](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)として入手できます。
*   **SQLite**
    *   System.Data.SQLite は [NuGet パッケージ](https://www.nuget.org/packages/System.Data.SQLite/)として入手できます

この一覧に含まれていることは、EF6 用のビルドが使用可能になっていることのみを示し、特定のプロバイダーの機能またはサポートのレベルを示しているわけではないことに注意してください。

## <a name="registering-ef-providers"></a>EF プロバイダーの登録

Entity Framework 6 EF プロバイダー以降は、コード ベースの構成を使用して、またはアプリケーションの構成ファイルで登録できます。

### <a name="config-file-registration"></a>構成ファイルの登録

app.config または web.config での EF プロバイダーの登録には、次の形式があります。


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

多くの場合、NuGet から EF プロバイダーがインストールされている場合、NuGet パッケージがこの登録を構成ファイルに自動的に追加します。 アプリのスタートアップ プロジェクトではないプロジェクトに NuGet パッケージをインストールする場合は、登録をスタートアップ プロジェクトの構成ファイルにコピーする必要があります。

この登録で "invariantName" は、ADO.NET プロバイダーを識別するために使用されるものと同じインバリアント名です。 これは、DbProviderFactories 登録の “invariant” 属性として、および接続文字列の登録の "providerName" 属性として確認できます。 使用するインバリアント名は、プロバイダーのドキュメントにも含める必要があります。 インバリアント名の例として、SQL Server を示す "System.Data.SqlClient" や、SQL Server Compact を示す "System.Data.SqlServerCe.4.0" などがあります。

この登録の "type" は、"System.Data.Entity.Core.Common.DbProviderServices" から取得されるプロバイダーの種類のアセンブリ修飾名です。 たとえば、SQL Compact に使用する文字列は、“System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact” です。 ここで使用する型は、プロバイダーのドキュメントに含まれている必要があります。

### <a name="code-based-registration"></a>コード ベースの登録

Entity Framework 6 以降では、EF のアプリケーション全体の構成をコードで指定できます。 詳細については、「_[Entity Framework - コード ベースの構成](https://msdn.microsoft.com/data/jj680699)_」を参照してください。 コード ベースの構成を使用して、EF プロバイダーを登録する通常の方法では、System.Data.Entity.DbConfiguration から派生する新しいクラスを作成し、DbContext クラスと同じアセンブリ内にそれを配置します。 その後で、DbConfiguration クラスが、そのコンストラクターでプロバイダーを登録する必要があります。 たとえば、SQL Compact プロバイダーを登録するための DbConfiguration クラスは次のようになります。

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

このコードでは、"SqlCeProviderServices.ProviderInvariantName" は、SQL Server Compact プロバイダーのインバリアント名文字列 ("System.Data.SqlServerCe.4.0") の簡易版であり、SqlCeProviderServices.Instance は、SQL Compact EF プロバイダーのシングルトン インスタンスを返します。

## <a name="what-if-the-provider-i-need-isnt-available"></a>必要なプロバイダーが使用できない場合

以前のバージョンの EF でプロバイダーを使用できる場合、プロバイダーの所有者に連絡して、EF6 のバージョンを作成するように依頼してください。 [EF6 のプロバイダー モデルに関するドキュメント](xref:ef6/fundamentals/providers/provider-model)の参照を含める必要があります。

## <a name="can-i-write-a-provider-myself"></a>自分でプロバイダーを作成することはできますか

EF のプロバイダーを自分で作成することは可能ですが、簡単なこととは考えないでください。 前述の EF6 のプロバイダー モデルに関するリンクは、出発点として適しています。 [EF のオープン ソース コードベース](https://github.com/aspnet/EntityFramework6)に含まれている SQL Server および SQL CE プロバイダーのコードを開始点または参照として使用すると役に立つことがあります。

EF6 以降、EF プロバイダーと基になる ADO.NET プロバイダーとの結びつきが緩やかになっていることに注意してください。 これにより、ADO.NET クラスの書き込みまたはラップを行わずに、EF プロバイダーを簡単に記述できるようになりました。
