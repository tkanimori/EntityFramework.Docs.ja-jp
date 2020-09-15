---
title: コードベースの構成-EF6
description: Entity Framework 6 のコードベースの構成
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070732"
---
# <a name="code-based-configuration"></a>コードベースの構成
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

Entity Framework アプリケーションの構成は、構成ファイル (app.config/web.config) またはコードを使用して指定できます。 後者は、コードベースの構成と呼ばれます。  

構成ファイルの構成については、 [別の記事](xref:ef6/fundamentals/configuring/config-file)で説明します。 構成ファイルは、コードベースの構成よりも優先されます。 つまり、構成オプションがコードと構成ファイルの両方で設定されている場合、構成ファイルの設定が使用されます。  

## <a name="using-dbconfiguration"></a>DbConfiguration の使用  

EF6 以降のコードベースの構成は、System.Data.Entity.Config のサブクラスを作成することによって実現されます。DbConfiguration. DbConfiguration をサブクラス化する場合は、次のガイドラインに従う必要があります。  

- アプリケーションの DbConfiguration クラスを1つだけ作成します。 このクラスは、アプリドメイン全体の設定を指定します。  
- DbConfiguration クラスを Dbconfiguration クラスと同じアセンブリに配置します。 (これを変更する場合は、「 *DbConfiguration の移動* 」セクションを参照してください)。  
- DbConfiguration クラスにパブリックのパラメーターなしのコンストラクターを指定します。  
- このコンストラクター内から保護された DbConfiguration メソッドを呼び出すことによって、構成オプションを設定します。  

これらのガイドラインに従うことで、EF は、モデルにアクセスする必要があるツールとアプリケーションの実行時の両方で、構成を自動的に検出して使用できるようになります。  

## <a name="example"></a>例  

DbConfiguration から派生したクラスは次のようになります。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

このクラスは、SQL Azure の実行戦略を使用するように EF を設定します。これにより、失敗したデータベース操作を自動的に再試行し、Code First から規則によって作成されたデータベースに対してローカル DB を使用します。  

## <a name="moving-dbconfiguration"></a>DbConfiguration の移動  

DbConfiguration クラスを Dbconfiguration クラスと同じアセンブリに配置できない場合があります。 たとえば、それぞれ異なるアセンブリに2つの DbContext クラスがあるとします。 この処理には、2つのオプションがあります。  

1つ目のオプションは、構成ファイルを使用して、使用する DbConfiguration インスタンスを指定する方法です。 これを行うには、entityFramework セクションの codeConfigurationType 属性を設定します。 次に例を示します。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType の値は、アセンブリおよび DbConfiguration クラスの名前空間修飾名である必要があります。  

2つ目の方法は、コンテキストクラスに DbConfigurationTypeAttribute を配置することです。 次に例を示します。  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

属性に渡す値は、上に示すように DbConfiguration 型にするか、アセンブリと名前空間で修飾された型名の文字列にすることができます。 次に例を示します。  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>DbConfiguration を明示的に設定する  

DbContext 型を使用する前に構成が必要になる状況もあります。 この例には以下のようなものがあります。  

- DbModelBuilder を使用してコンテキストなしでモデルを構築する  
- アプリケーションコンテキストが使用される前にそのコンテキストが使用される DbContext を利用する他のフレームワーク/ユーティリティコードを使用する  

このような状況では、EF は構成を自動的に検出できないため、代わりに次のいずれかを実行する必要があります。  

- 上の「 *DbConfiguration の移動* 」セクションで説明したように、構成ファイルで dbconfiguration 型を設定します。
- アプリケーションの起動時に、静的な DbConfiguration. SetConfiguration メソッドを呼び出します。  

## <a name="overriding-dbconfiguration"></a>DbConfiguration のオーバーライド  

DbConfiguration で構成セットを上書きする必要がある状況もあります。 これは通常、アプリケーション開発者によって行われるのではなく、派生した DbConfiguration クラスを使用できないサードパーティプロバイダーやプラグインによって実行されます。  

このため、EntityFramework では、ロックダウンの直前に既存の構成を変更できるイベントハンドラーを登録できます。  また、EF サービスロケーターによって返されるサービスを置き換えるための、砂糖の方法も提供します。 これは、次のように使用することを意図しています。  

- (EF が使用される前に) アプリの起動時に、プラグインまたはプロバイダーがこのイベントのイベントハンドラーメソッドを登録する必要があります。 (これは、アプリケーションで EF を使用する前に行う必要があることに注意してください)。  
- イベントハンドラーは、置き換える必要があるすべてのサービスに対して、交換用 Eservice を呼び出します。  

たとえば、IDbConnectionFactory と DbProviderService を置き換えるには、次のようなハンドラーを登録します。  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

上記のコードでは、MyProviderServices と Myproviderservices がサービスの実装を表しています。  

また、依存関係ハンドラーを追加して同じ効果を得ることもできます。  

この方法で DbProviderFactory をラップすることもできますが、これを行うと EF にのみ影響し、EF の外部では DbProviderFactory を使用しないことに注意してください。 このため、以前と同じように引き続き DbProviderFactory をラップすることをお勧めします。  

また、パッケージマネージャーコンソールから移行を実行する場合など、アプリケーションの外部で実行するサービスについても考慮する必要があります。 コンソールから移行を実行すると、DbConfiguration の検索が試行されます。 ただし、ラップされたサービスを取得するかどうかは、イベントハンドラーの登録場所によって異なります。 DbConfiguration の構築の一部として登録されている場合は、コードを実行し、サービスをラップする必要があります。 通常、これは当てはまりません。これは、ツールがラップされたサービスを取得しないことを意味します。  
