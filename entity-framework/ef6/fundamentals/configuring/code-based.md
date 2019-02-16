---
title: コード ベースの構成 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: c317f112f713612f7b9aef3764a0bd004fef5424
ms.sourcegitcommit: 735715f10cc8a231c213e4f055d79f0effd86570
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325354"
---
# <a name="code-based-configuration"></a>コード ベースの構成
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

構成ファイル (app.config/web.config) またはコードによって、Entity Framework アプリケーションの構成を指定できます。 後者の場合は、コード ベースの構成と呼ばれます。  

構成ファイル内の構成については、「、[別の記事](config-file.md)します。 構成ファイルは、コード ベースの構成よりも優先されます。 つまり、コードの両方でと、構成ファイルで、構成オプションが設定されている場合は、構成ファイルで設定が使用されます。  

## <a name="using-dbconfiguration"></a>DbConfiguration を使用します。  

EF6 と上記のコード ベースの構成は、System.Data.Entity.Config.DbConfiguration のサブクラスを作成することによって実現されます。 次のガイドラインは、DbConfiguration をサブクラス化するとき、その後にする必要があります。  

- アプリケーションの 1 つだけの DbConfiguration クラスを作成します。 このクラスには、アプリ ドメイン全体の設定を指定します。  
- DbContext クラスと同じアセンブリ内の DbConfiguration クラスを配置します。 (を参照してください、*移動 DbConfiguration*セクションを変更する場合)。  
- DbConfiguration クラスのパブリック コンス トラクターを提供します。  
- このコンス トラクター内からの保護の DbConfiguration メソッドを呼び出すことによって、構成オプションを設定します。  

これらのガイドラインに従うと、EF を探して、モデルにアクセスする必要があると、アプリケーションを実行するときに両方のツールで、構成を自動的に使用できます。  

## <a name="example"></a>例  

DbConfiguration から派生したクラスは、次のようになります。  

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

このクラスは、- 失敗したデータベースの操作を自動的に再試行して、Code First の規則によって作成されるデータベースのローカル DB を使用して、SQL Azure の実行方法 - を使用する EF を設定します。  

## <a name="moving-dbconfiguration"></a>DbConfiguration の移動  

可能性がありますが、DbContext クラスと同じアセンブリ内の DbConfiguration クラスを配置することはできません。 たとえば、2 つ DbContext クラスにはそれぞれを異なるアセンブリがあります。 これを処理するための 2 つのオプションがあります。  

最初のオプションでは、使用する DbConfiguration インスタンスを指定する構成ファイルを使用します。 これを行うには、entityFramework セクションの codeConfigurationType 属性を設定します。 例:  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType の値は、アセンブリと DbConfiguration クラスの名前空間の修飾名である必要があります。  

2 番目のオプションは、DbConfigurationTypeAttribute をコンテキスト クラスに配置することです。 例:  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

使用できます、DbConfiguration 型では、上記の属性に値が渡されるか、アセンブリと名前空間の修飾型名の文字列。 例:  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>DbConfiguration を明示的に設定します。  

状況によっては、前に、任意の DbContext 型が使用されている構成を必要になる可能性があります。 この追加の例:  

- DbModelBuilder を使用して、コンテキストなしのモデルを構築するには  
- アプリケーションのコンテキストを使用する前にそのコンテキストが使用されている DbContext を使用するその他のフレームワーク/ユーティリティ コードを使用します。  

このような状況も EF は、構成を自動的に検出することと、次のいずれかの代わりに操作する必要があります。  

- 」の説明に従って、構成ファイルで DbConfiguration 型を設定、*移動 DbConfiguration*前のセクション
- アプリケーションの起動時に静的 DbConfiguration.SetConfiguration メソッドを呼び出す  

## <a name="overriding-dbconfiguration"></a>DbConfiguration をオーバーライドします。  

状況によっては、DbConfiguration で設定された構成をオーバーライドする必要があります。 これではなく、サード パーティ プロバイダーとプラグイン派生 DbConfiguration クラスを使用することはできませんが、アプリケーション開発者が通常行いますされません。  

これは、EntityFramework がロック ダウンする前に、既存の構成を変更できるイベント ハンドラーを登録できます。  また、EF のサービス ロケーターによって返される任意のサービスを置き換えるためには、具体的には、sugar メソッドも提供します。 これは意図された使用する方法です。  

- (前に EF を使用すると)、アプリの起動時に、プラグインまたはプロバイダーがこのイベントのイベント ハンドラー メソッドを登録する必要があります。 (アプリケーションは、EF を使用する前にこれ行う必要がありますに注意してください)。  
- イベント ハンドラーは、ReplaceService への呼び出しを置き換える必要があるすべてのサービスです。  

たとえば、repalce IDbConnectionFactory を DbProviderService は、次のようにハンドラーを登録しました。  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

MyProviderServices と MyConnectionFactory 上のコードでは、サービスの実装を表します。  

同じ効果を取得する追加の依存関係のハンドラーを追加することもできます。  

この方法で DbProviderFactory をラップすることもできますが、行うのためにのみ反映 EF と EF の外部で DbProviderFactory の使用しないことに注意してください。 このため、引き続き DbProviderFactory をラップする前にある、したいがおそらくします。  

必要がありますも留意するサービスを実行するアプリケーションの外部になど、パッケージ マネージャー コンソールからの移行を実行する場合。 実行すると、DbConfiguration を検索しようとする、コンソールから移行します。 ただし、where に依存かどうかを取得する、ラップされたサービスに登録されているイベント ハンドラー。 DbConfiguration の構築の一部として登録されている場合は、コードを実行する必要があり、サービスをラップする必要がありますを取得します。 通常このすることはできませんし、つまりツールは、ラップされたサービスを取得しません。  
