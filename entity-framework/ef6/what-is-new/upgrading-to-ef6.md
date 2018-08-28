---
title: Entity Framework 6 へのアップグレード
author: divega
ms.date: 2016-10-23
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 8317fc0dcd5a7b176f3100e449bc6ff708bd310f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997372"
---
# <a name="upgrading-to-entity-framework-6"></a>Entity Framework 6 へのアップグレード

以前のバージョンの EF コードは、.NET Framework の一部として出荷されるコア ライブラリ (主に system.data.entity.dll 内) と NuGet パッケージに付属して帯域外の (OOB) ライブラリ (主に EntityFramework.dll) の間で分割が。 EF6 では、コア ライブラリからコードを取得し、OOB ライブラリを組み込みます。 これはオープン ソースに EF を許可するために必要と .NET Framework からのさまざまなペースで進化できるようにします。 この結果は、アプリケーションが移動された型に対して再構築する必要があることです。

これは、EF 4.1 以降を発送済みとしての DbContext を使用しているアプリケーションの簡単なはずです。 ObjectContext を使用するアプリケーションの必要なもう少し作業がまだは難しくはありません。

既存のアプリケーションを EF6 にアップグレードするために必要のある事柄のチェックリストを次に示します。

## <a name="1-install-the-ef6-nuget-package"></a>1.EF6 の NuGet パッケージをインストールします。

新しい Entity Framework 6 のランタイムをアップグレードする必要があります。

1. クリックし、プロジェクトを右クリックして**NuGet パッケージの管理.**  
2. 下、**オンライン** タブを選択**EntityFramework**クリック**インストール**  
   > [!NOTE]
   > 以前のバージョンの EntityFramework NuGet パッケージがインストールされている場合は、EF6 にアップグレードこのされます。

または、パッケージ マネージャー コンソールから、次のコマンドを実行できます。

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2.System.Data.Entity.dll へのアセンブリ参照を削除することを確認します。

EF6 の NuGet パッケージをインストールする必要があります自動的に削除 System.Data.Entity へのプロジェクトから参照できます。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3.EF 6.x のコード生成を使用するすべての EF デザイナー (EDMX) モデルをスワップします。

EF Designer で作成されたモデルがあれば、EF6 の互換性のあるコードを生成するコード生成テンプレートを更新する必要があります。

> [!NOTE]
> 現在は EF 6.x DbContext ジェネレーター テンプレートのみ Visual Studio 2012 および 2013 を使用できます。

1. 既存のコード生成テンプレートを削除します。 これらのファイルの名前は通常 **\<edmx_file_name\>.tt**と **\<edmx_file_name\>します。Context.tt**ソリューション エクスプ ローラーで、edmx ファイルの下に入れ子にするとします。 キーを押して、ソリューション エクスプ ローラーで、テンプレートを選択することができます、 **Del**それらを削除するキー。  
   > [!NOTE]
   > Web サイト プロジェクトで、テンプレートはことはありません、edmx ファイルの下で入れ子になったがソリューション エクスプ ローラーで一緒に表示します。  

   > [!NOTE]
   > VB.NET プロジェクトでは、' すべてのファイル ' 入れ子になったテンプレート ファイルを参照できるを有効にする必要があります。
2. 適切な EF 6.x のコード生成テンプレートを追加します。 デザイン画面を選択しますを右クリックが EF デザイナーでモデルを開く**コード生成項目の追加.**
    - (推奨)、DbContext API を使用しているかどうかは**EF 6.x DbContext ジェネレーター**で使用できるが、**データ** タブ。  
      > [!NOTE]
      > Visual Studio 2012 を使用している場合は、このテンプレートを使用して EF 6 ツールをインストールする必要があります。 参照してください[Entity Framework の取得](~/ef6/fundamentals/install.md)詳細についてはします。  

    - ObjectContext API を使用しているかどうかは、選択する必要があります、**オンライン**タブし、検索**EF 6.x EntityObject ジェネレーター**します。  
3. コード生成テンプレートをすべてのカスタマイズを適用した場合は、更新されたテンプレートに再適用する必要があります。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4.使用されている任意の中核となる EF 型の名前空間を更新します。

DbContext とコードの最初の型の名前空間は変更されていません。 つまり、EF 4.1 を使用する多くのアプリケーションまたは後で何も変更する必要はありません。

System.data.entity.dll 内に含まれていた ObjectContext などの型は、新しい名前空間に移動されました。 つまり、更新する必要があります、*を使用して*または*インポート*EF6 に対してビルドするためのディレクティブ。

名前空間の変更の一般的な規則は、System.Data.* で任意の型が System.Data.Entity.Core.* に移動されます。 つまり、挿入**Entity.Core します。** System.Data 後。 例えば:

- System.Data.EntityException = > System.Data します。**Entity.Core します。** EntityException  
- System.Data.Objects.ObjectContext = > System.Data します。**Entity.Core します。** Objects.ObjectContext  
- System.Data.Objects.DataClasses.RelationshipManager = > System.Data します。**Entity.Core します。** Objects.DataClasses.RelationshipManager  

これらの種類が、 *Core*名前空間 DbContext ベースのほとんどのアプリケーションを直接使用されていないためです。 System.data.entity.dll 内の一部であった一部の種類は引き続き使用一般的と直接 DbContext ベースのアプリケーションとためありませんに移動されました、 *Core*名前空間。 これらの数値は、次のとおりです。

- System.Data.EntityState = > System.Data します。**エンティティ。** EntityState  
- System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data します。**Entity.DbFunctionAttribute**  
  > [!NOTE]
  > このクラスの名前が変更されました古い名前のクラスは引き続き存在して、動作しますが、ここで不使用とマークします。  
- System.Data.Objects.EntityFunctions = > System.Data します。**Entity.DbFunctions**  
  > [!NOTE]
  > このクラスの名前が変更されました古い名前のクラスは引き続き存在して、動作しますが、現在不使用とマークされ)。  
- System.Data.Spatial から移動空間のクラス (たとえば、DbGeography、DbGeometry) = > System.Data します。**エンティティ。** 空間

> [!NOTE]
> System.Data 名前空間の一部の型は System.Data.dll EF アセンブリではないです。 これらの型に移動していないと、そのため、名前空間は変更されません。
