---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.date: 01/07/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e70011b42818e4df1ec5b9b88d7adb9d36bb26f1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654797"
---
# <a name="ef-core-tools--extensions"></a>EF Core のツールと拡張機能

以下のツールと拡張機能は、Entity Framework Core 2.0 およびそれ以降の追加機能を提供します。

> [!IMPORTANT]  
> 拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。

## <a name="tools"></a>ツール

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。 これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。

[Web サイト](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザインです。 モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。

[Web サイト](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a>EF Core のパワー ツール

EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio 2017 の拡張機能です。 既存のデータベースと [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。

[GitHub の Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework のビジュアル エディター

Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの ORM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。 コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。 継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。

[GitHub リポジトリ](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft の Entity Framework Core Generator

Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。 このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。

[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>拡張機能

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。

[GitHub リポジトリ](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a>Microsoft.EntityFrameworkCore.DynamicLinq

EF Core との非同期サポートが含まれる System.Linq.Dynamic の .NET Core/.NET Standard ポート。
コードではなく文字列式から動的に LINQ クエリを構築する方法を示す、Microsoft のサンプルから生成された System.Linq.Dynamic。

[GitHub リポジトリ](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efsecondlevelcachecore"></a>EFSecondLevelCache.Core

同じクエリの後続の実行によってデータベースにアクセスされたり、キャッシュからデータが直接取得されたりしないように、2 番目のレベルのキャッシュに EF Core クエリの結果が格納されるようにする拡張機能。

[GitHub リポジトリ](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="entityframeworkcoreprimarykey"></a>EntityFrameworkCore.PrimaryKey

このライブラリにより、すべてのエンティティから主キー (複合キーを含む) の値をディクショナリとして取得できるようになります。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcoretypedoriginalvalues"></a>EntityFrameworkCore.TypedOriginalValues

このライブラリにより、エンティティ プロパティの元の値に対し、厳密に型指定されたアクセスができるようになります。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a>Geco

Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。 Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。 シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。

[GitHub リポジトリ](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a>LinqKit.Microsoft.EntityFrameworkCore

LinqKit.Microsoft.EntityFrameworkCore は、LINQKit ライブラリの EF Core 互換バージョンです。 LINQKit は、LINQ to SQL および Entity Framework のパワー ユーザー向けの無料の拡張機能セットです。 述語式の動的な構築やサブクエリでの式の変数の使用などの拡張機能が有効になります。  

[GitHub リポジトリ](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。

[GitHub リポジトリ](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。

[GitHub リポジトリ](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

一括操作 (挿入、更新、削除) の EF Core 拡張機能。

[GitHub リポジトリ](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

EF Core へのデザイン時の複数形化の追加。

[GitHub リポジトリ](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="pomelofoundationpomeloentityframeworkcoreextensionstosql"></a>PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql

EF Core が単純なシナリオの指定の LINQ クエリに対して生成する SQL ステートメントを取得する単純な拡張メソッド。 EF Core は 1 つの LINQ クエリに対して複数の SQL ステートメントを生成したり、パラメータ値に応じて異なる SQL ステートメントを生成したりできるため、ToSql メソッドは単純なシナリオに限定されます。

[GitHub リポジトリ](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

EF Core の [Index] 属性のリバイバルです (モデル構築のための拡張機能)。

[GitHub リポジトリ](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。 リレーショナル プロバイダーのような動作になります。

[GitHub リポジトリ](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

EF Core のテンポラル サポートの実装。

[GitHub リポジトリ](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

EF Core の高パフォーマンスな 2 番目のレベルのクエリ キャッシュ。

[GitHub リポジトリ](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a>Entity Framework Plus

次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。

[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework Extensions

高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。

[Web サイト](https://entityframework-extensions.net/)

### <a name="reconciler"></a>調整

それぞれのエンティティを挿入、更新、および削除することにより、ストア内のエンティティ グラフを更新します。

[GitHub リポジトリ](https://github.com/jtheisen/reconciler)
