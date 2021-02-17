---
title: コードベースの構成-EF6
description: Entity Framework 6 のコードベースの構成
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: b16cbcef85708730dcc6b82a38635cc60cb2206a
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543173"
---
# <a name="code-based-configuration"></a>コードベースの構成
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

Entity Framework アプリケーションの構成は、構成ファイル (app.config/web.config) またはコードを使用して指定できます。 後者は、コードベースの構成と呼ばれます。  

構成ファイルの構成については、 [別の記事](xref:ef6/fundamentals/configuring/config-file)で説明します。 構成ファイルは、コードベースの構成よりも優先されます。 つまり、構成オプションがコードと構成ファイルの両方で設定されている場合、構成ファイルの設定が使用されます。  

## <a name="using-dbconfiguration"></a>`DbConfiguration` の使用

EF6 以降のコードベースの構成は、のサブクラスを作成することによって実現され `System.Data.Entity.Config.DbConfiguration` ます。 サブクラス化する場合は、次のガイドラインに従う必要があり `DbConfiguration` ます。  

- `DbConfiguration`アプリケーションのクラスを1つだけ作成します。 このクラスは、アプリドメイン全体の設定を指定します。  
- クラス `DbConfiguration` をクラスと同じアセンブリに配置し `DbContext` ます。 (これを変更する場合は、「*移動 `DbConfiguration`* 」セクションを参照してください)。  
- クラスに `DbConfiguration` パブリックなパラメーターなしのコンストラクターを指定します。  
- このコンストラクター内から保護されたメソッドを呼び出すことによって、構成オプションを設定 `DbConfiguration` します。  

これらのガイドラインに従うことで、EF は、モデルにアクセスする必要があるツールとアプリケーションの実行時の両方で、構成を自動的に検出して使用できるようになります。  

## <a name="example"></a>例  

から派生したクラスは次の `DbConfiguration` ようになります。  

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

## <a name="moving-dbconfiguration"></a>動き `DbConfiguration`  

クラスと同じアセンブリにクラスを配置できない場合があり `DbConfiguration` `DbContext` ます。 たとえば、2つのクラスが `DbContext` それぞれ異なるアセンブリに存在する場合があります。 この処理には、2つのオプションがあります。  

最初のオプションでは、構成ファイルを使用して、 `DbConfiguration` 使用するインスタンスを指定します。 これを行うには、entityFramework セクションの codeConfigurationType 属性を設定します。 次に例を示します。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType の値は、クラスのアセンブリおよび名前空間修飾名である必要があり `DbConfiguration` ます。  

2番目のオプションは、コンテキストクラスに配置することです `DbConfigurationTypeAttribute` 。 次に例を示します。  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

属性に渡される値は、 `DbConfiguration` 上に示すように型にするか、アセンブリと名前空間で修飾された型名の文字列にすることができます。 次に例を示します。  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>設定 ( `DbConfiguration` 明示的に)  

型を使用する前に構成が必要になる状況もあり `DbContext` ます。 この例には以下のようなものがあります。  

- を使用して `DbModelBuilder` コンテキストなしでモデルを構築する  
- `DbContext`アプリケーションコンテキストが使用される前にコンテキストが使用されるを利用する他のフレームワーク/ユーティリティコードを使用する  

このような状況では、EF は構成を自動的に検出できないため、代わりに次のいずれかを実行する必要があります。  

- 上の `DbConfiguration` 「 *`DbConfiguration` 移動*」セクションで説明したように、構成ファイルで型を設定します。
- 静的を呼び出し `DbConfiguration` ます。アプリケーションの起動中の SetConfiguration メソッド  

## <a name="overriding-dbconfiguration"></a>オーバーライド `DbConfiguration`  

で構成セットを上書きする必要がある状況もあり `DbConfiguration` ます。 これは通常、アプリケーション開発者によって行われるのではなく、派生クラスを使用できないサードパーティプロバイダーやプラグインによって実行され `DbConfiguration` ます。  

このため、EntityFramework では、ロックダウンの直前に既存の構成を変更できるイベントハンドラーを登録できます。  また、EF サービスロケーターによって返されるサービスを置き換えるための、砂糖の方法も提供します。 これは、次のように使用することを意図しています。  

- (EF が使用される前に) アプリの起動時に、プラグインまたはプロバイダーがこのイベントのイベントハンドラーメソッドを登録する必要があります。 (これは、アプリケーションで EF を使用する前に行う必要があることに注意してください)。  
- イベントハンドラーは、置き換える必要があるすべてのサービスに対して、交換用 Eservice を呼び出します。  

たとえば、を置き換えるには、次の `IDbConnectionFactory` `DbProviderService` ようにハンドラーを登録します。  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

上記のコードでは、 `MyProviderServices` とは `MyConnectionFactory` サービスの実装を表しています。  

また、依存関係ハンドラーを追加して同じ効果を得ることもできます。  

この方法でラップすることもでき `DbProviderFactory` ますが、これを行うと ef にのみ影響し、 `DbProviderFactory` ef の外部では使用されません。 このため、以前と同じように、引き続きラップする必要があり `DbProviderFactory` ます。  

また、パッケージマネージャーコンソールから移行を実行する場合など、アプリケーションの外部で実行するサービスについても考慮する必要があります。 コンソールから移行を実行すると、の検索が試行され `DbConfiguration` ます。 ただし、ラップされたサービスを取得するかどうかは、イベントハンドラーの登録場所によって異なります。 の構築の一部として登録されている場合 `DbConfiguration` 、コードを実行し、サービスをラップする必要があります。 通常、これは当てはまりません。これは、ツールがラップされたサービスを取得しないことを意味します。  
