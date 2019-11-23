---
title: Entity Framework 6-EF6 へのアップグレード
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 4395a9c117a6cf38e7fc08f11ee689d6fffa6fed
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182097"
---
# <a name="upgrading-to-entity-framework-6"></a>Entity Framework 6 へのアップグレード

以前のバージョンの EF では、コードは、NuGet パッケージに付属している .NET Framework および帯域外 (OOB) ライブラリ (主に EntityFramework .dll) の一部として出荷されたコアライブラリ (主に system.object) 間で分割されました。 EF6 はコアライブラリからコードを取得し、OOB ライブラリに組み込みます。 これは、EF をオープンソースにして、.NET Framework とは異なるペースで進化できるようにするために必要でした。 その結果、移動された型に対してアプリケーションを再構築する必要があります。

これは、EF 4.1 以降に付属している DbContext を使用するアプリケーションでは簡単です。 ObjectContext を使用するアプリケーションには、もう少し作業が必要ですが、それでも難しいことはありません。

既存のアプリケーションを EF6 にアップグレードするために必要な作業のチェックリストを次に示します。

## <a name="1-install-the-ef6-nuget-package"></a>1. EF6 NuGet パッケージをインストールする

新しい Entity Framework 6 ランタイムにアップグレードする必要があります。

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理...]** を選択します。  
2. **[オンライン]** タブで **[entityframework]** を選択し、 **[インストール]** をクリックします。  
   > [!NOTE]
   > 以前のバージョンの EntityFramework NuGet パッケージがインストールされている場合は、EF6 にアップグレードされます。

または、パッケージマネージャーコンソールから次のコマンドを実行します。

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. system.object へのアセンブリ参照が削除されていることを確認します。

EF6 NuGet パッケージをインストールすると、プロジェクトからへの参照が自動的に削除されます。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. ef Designer (EDMX) モデルをスワップして EF 6.x コード生成を使用する

EF デザイナーで作成されたモデルがある場合は、コード生成テンプレートを更新して、EF6 互換コードを生成する必要があります。

> [!NOTE]
> 現在、Visual Studio 2012 および2013で使用できる EF 6.x DbContext ジェネレーターテンプレートのみがあります。

1. 既存のコード生成テンプレートを削除します。 これらのファイルは、通常、 **\>edmx_file_name\<** と\<edmx_file_name\>に名前が付けられます **。Context.tt**は、ソリューションエクスプローラーの edmx ファイルの下に入れ子になっています。 ソリューションエクスプローラーでテンプレートを選択し、 **del**キーを押して削除することができます。  
   > [!NOTE]
   > Web サイトプロジェクトでは、テンプレートは edmx ファイルの下に入れ子になっていませんが、ソリューションエクスプローラーに並べられています。  

   > [!NOTE]
   > VB.NET プロジェクトでは、[すべてのファイルを表示] を有効にして、入れ子になったテンプレートファイルを表示できるようにする必要があります。
2. 適切な EF 6.x コード生成テンプレートを追加します。 EF デザイナーでモデルを開き、デザイン画面を右クリックして、 **[コード生成項目の追加...]** を選択します。
    - DbContext API (推奨) を使用している場合は、 **[データ]** タブで**EF 6.X の dbcontext ジェネレーター**を使用できます。  
      > [!NOTE]
      > Visual Studio 2012 を使用している場合は、このテンプレートを使用するために EF 6 ツールをインストールする必要があります。 詳細については、「 [Get Entity Framework](~/ef6/fundamentals/install.md) 」を参照してください。  

    - ObjectContext API を使用している場合は、 **[オンライン]** タブを選択し、 **EF 6.X の entityobject Generator**を検索する必要があります。  
3. コード生成テンプレートにカスタマイズを適用した場合は、更新されたテンプレートに再適用する必要があります。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. 使用されているすべてのコア EF 型の名前空間を更新する

DbContext および Code First 型の名前空間は変更されていません。 これは、EF 4.1 以降を使用する多くのアプリケーションでは、何も変更する必要がないことを意味します。

以前に system.object に含まれていた ObjectContext などの型は、新しい名前空間に移動されました。 つまり、EF6 に対してビルドするには、 *using*ディレクティブまたは*Import*ディレクティブを更新する必要があります。

名前空間の変更に関する一般的な規則は、system.string 内のすべての型が、system.string に移動されることです。 言い換えると、" **Entity. Core** " を挿入するだけです。 System. Data の後。 例 :

- System.string. EntityException = > system.string。**Entity. Core**。EntityException  
- System.string は、system.string を > します。**Entity. Core**。オブジェクト。 ObjectContext  
- RelationshipManager = > system.string のデータを持つことができます。**Entity. Core**。オブジェクト. RelationshipManager  

これらの型は、ほとんどの DbContext ベースのアプリケーションでは直接使用されないため、*コア*名前空間にあります。 System.object の一部であった型の中には、DbContext ベースのアプリケーションで一般的に使用されているものもあります。そのため、*コア*名前空間に移動されていません。 これらの設定は、次のとおりです。

- EntityState = > system.string をします。**エンティティ**。EntityState  
- System.string. EdmFunctionAttribute = > system.string......**Entity. DbFunctionAttribute**  
  > [!NOTE]
  > このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています。  
- System.string. EntityFunctions = > system.string です。**Entity DbFunctions**  
  > [!NOTE]
  > このクラスは名前が変更されました。古い名前のクラスは引き続き存在し、動作しますが、現在は不使用とマークされています)。  
- 空間クラス (例: DbGeography, Dbgeography) は、system.string > から移動しています。**エンティティ**。許容

> [!NOTE]
> System.string 名前空間の一部の型は、EF アセンブリではない system.string に含まれています。 これらの型は移動されていないため、名前空間は変更されません。
