---
title: ツールと拡張機能 - EF Core
description: Entity Framework Core の外部ツールと拡張機能
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e8198e0b0a51968336be97ea7d7a4b65e856d32f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618020"
---
# <a name="ef-core-tools--extensions"></a>EF Core のツールと拡張機能

以下のツールと拡張機能は、Entity Framework Core 2.1 およびそれ以降の追加機能を提供します。

> [!IMPORTANT]  
> 拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。 特に、古いバージョンの EF Core 用に構築された拡張機能は、最新バージョンで動作させる前に更新が必要になる場合があります。

## <a name="tools"></a>ツール

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。 これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。 対象の EF Core:2、3

[Web サイト](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザインです。 モデル ファーストまたはデータベース ファーストのアプローチを使用した EF Core モデルの視覚的なデザイン、そして C# または Visual Basic のコード生成をサポートします。 対象の EF Core:1、2、3、5。

[Web サイト](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>Entity Framework 用の nHydrate ORM

厳密に型指定された、Entity Framework 用に拡張できるクラスを作成する ORM です。 生成されたコードは Entity Framework Core になります。 これらに違いはありません。 これは、EF またはカスタム ORM に代わるものではありません。 チームが複雑なデータベース スキーマを管理できるビジュアルのモデリング レイヤーです。 これは、Git のような SCM ソフトウェアに適しており、最小限の競合でご利用のモデルへのマルチ ユーザー アクセスが可能になります。 インストーラーによって、モデルの変更が追跡され、アップグレード スクリプトが作成されます。 対象の EF Core:3.

[GitHub サイト](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core のパワー ツール

EF Core のパワー ツールは、シンプルなユーザー インターフェイスで EF Core デザイン時のさまざまなタスクを公開する Visual Studio の拡張機能です。 既存のデータベースと [SQL Server DACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) からの DbContext とエンティティ クラスのリバース エンジニアリング、データベース移行の管理、モデルの視覚化が含まれます。 対象の EF Core:2、3。

[GitHub の Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework のビジュアル エディター

Entity Framework のビジュアル エディターは、EF 6 のビジュアル デザインの ORM デザイナーと EF Core のクラスを追加する、Visual Studio の拡張機能です。 コードは T4 テンプレートを使用して生成されるため、あらゆるニーズに合わせてカスタマイズできます。 継承、一方向および双方向の関連付け、列挙体がサポートされ、クラスの色分けが可能になり、潜在的な設計の難解な部分を説明するテキストのブロックを追加できるようになります。 対象の EF Core:2.

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory は SQL Server データベースからの DbContext のクラス、エンティティ、マッピング構成、リポジトリ クラスの生成を自動化できる .NET Core のスキャフォールディング エンジンです。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft の Entity Framework Core Generator

Entity Framework Core Generator (efg) は既存のデータベースから EF Core モデルを生成できる .NET Core の CLI ツールで、`dotnet ef dbcontext scaffold` と非常に似ていますが、リージョンの置換やマッピング ファイルの解析による安全なコードの[再生成](https://efg.loresoft.com/en/latest/regeneration/)もサポートしています。 このツールは、ビュー モデル、検証、およびオブジェクト マッパー コードの生成をサポートしています。 対象の EF Core:2.

[チュートリアル](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[ドキュメント](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>拡張機能

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

EF Core によって実行されたデータ変更を履歴テーブルに自動的に記録できるプラグイン ライブラリ。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

第 2 レベルのキャッシュは、クエリ キャッシュです。 EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (ジェネレーター コンソール) は .NET Core 上で実行され、C# 補間の文字列を使用してコードを生成する、コンソール プロジェクトに基づいた単純なコード ジェネレーターです。 Geco には、複数形化、単数形化、編集可能なテンプレートのサポートが含まれる、EF Core のリバース モデル ジェネレーターが含まれています。 シード データ スクリプト ジェネレーター、スクリプト ランナー、データベース クリーナーも提供されます。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars 

Entity Framework Core ツールチェーンと Handlebars テンプレートを使用して、既存のデータベースからリバース エンジニアリングされたクラスのカスタマイズを可能にします。 対象の EF Core:2、3。

[GitHub リポジトリ](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore 

NeinLinq は、Entity Framework などの LINQ プロバイダーを拡張し、関数の再利用、クエリの書き直し、変換可能な述語とセレクターを使用した動的クエリの構築などを可能にします。 対象の EF Core:2、3。

[GitHub リポジトリ](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

一括操作 (挿入、更新、削除) の EF Core 拡張機能。 対象の EF Core:2、3。

[GitHub リポジトリ](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

デザイン時の複数形化を追加します。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

[Index] 属性のリバイバルです (モデル構築のための拡張機能を含む)。 対象の EF Core:2、3。

[GitHub リポジトリ](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

EF Core のメモリ内データベース プロバイダーに関するラッパーを提供します。 リレーショナル プロバイダーのような動作になります。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

テンポラル サポートの実装。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

導入された拡張メソッド `AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)` を使用して、任意のデータベース上で簡単にテンポラル クエリを実行します。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a>EFCore.TimeTraveler

EF Core コード、エンティティ、および既に定義済みのマッピングを使用して、[SQL Server テンポラル履歴](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)に対するフル機能の Entity Framework Core クエリを実行できるようにします。  コードを `using (TemporalQuery.AsOf(targetDateTime)) {...}` でラップすることにより、時間に関係なく使用できます。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

SQL Server を使用している開発者がテンポラル テーブルを簡単に使用できるようにする、Entity Framework Core の拡張ライブラリ。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

高パフォーマンスな第 2 レベルのクエリ キャッシュ。 対象の EF Core:2.

[GitHub リポジトリ](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

NCache Entity Framework Core Provider は、クエリの結果をキャッシュする、分散された第 2 レベルのキャッシュ プロバイダーです。 NCache の分散アーキテクチャにより、拡張性と可用性が向上しています。 対象は EF Core 2 です。

[Web サイト](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a>Entity Framework Plus

次のような機能によって DbContext を拡張します:Include Filter、Auditing、Caching、Query Future、Batch Delete、Batch Update など。 対象の EF Core:2、3。

[Web サイト](https://entityframework-plus.net/)
[GitHub リポジトリ](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework Extensions

高パフォーマンスの一括操作によって DbContext を拡張します:BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge など。 対象の EF Core:2、3。

[Web サイト](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

LINQ ラムダ式で拡張メソッドを呼び出すためのサポートを追加します。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a>XLinq

リレーショナル データベース用の統合言語クエリ (LINQ) テクノロジ。 C# を使用して、厳密に型指定されたクエリを記述できます。 対象の EF Core:3.

- クエリの作成時のラムダ、変数、関数内での複数のステートメントなどの C# の完全なサポート。
- SQL と意味的な違いはありません。 XLinq では SQL ステートメント (`SELECT`、`FROM`、`WHERE`) を第一級の C# メソッドとして、使い慣れた構文を intellisense、タイプ セーフおよびリファクタリングと組み合わせ宣言します。

その結果、SQL はその API をローカルに公開する、文字どおりの *"統合言語 SQL"* である "もう 1 つの" クラス ライブラリになります。

[Web サイト](http://xlinq.live/)

### <a name="ramses"></a>Ramses

ライフサイクル フック (SaveChanges 用)。 対象の EF Core:2、3。

[GitHub リポジトリ](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

これにより、snake_case を含むすべてのテーブル名と列名が自動的にすべて大文字またはすべて小文字の名前になります。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

NodaTime の SQL Server 用 EntityFrameworkCore にネイティブ サポートを追加します。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

Microsoft SQL Server テンポラル テーブル クエリをサポートするための Entity Framework Core 3.1 の LINQ 拡張機能。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

SQL Server EF Core プロバイダーに hierarchyid のサポートを追加します。 対象の EF Core:3.

[GitHub リポジトリ](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db.EntityFrameworkCore

SQL 式に対する LINQ クエリの代替トランスレーター。 対象の EF Core:3.

CTE、一括コピー、テーブル ヒント、ウィンドウ関数、一時テーブル、データベース側の作成、更新、削除作などの高度な SQL 機能のサポートが含まれています。

[GitHub リポジトリ](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

エンティティの論理的な削除のための実装。 対象の EF Core:3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)
