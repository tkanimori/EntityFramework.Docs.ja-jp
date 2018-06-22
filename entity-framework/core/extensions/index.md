---
title: ツールと拡張機能 - EF Core
author: ErikEJ
ms.author: divega
ms.date: 7/3/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/extensions/index
ms.openlocfilehash: 6c8cb3e0d8552f274118e4020b7e2e8009af7e11
ms.sourcegitcommit: fc68321c211aca38f7b9dc3a75677c6ca1b2524b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
ms.locfileid: "29769440"
---
# <a name="ef-core-tools--extensions"></a>EF Core のツールと拡張機能

ツールと拡張機能は、Entity Framework Core の追加機能を提供します。

> [!IMPORTANT]  
> 拡張機能はさまざまなソースから構築されており、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ拡張機能の利用を検討するとき、要件に合うよう、品質、使用許諾、互換性、サポートなどを必ず評価してください。

## <a name="tools"></a>ツール

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro は、Entity Framework と Entity Framework Core のサポートを備えたエンティティ モデリング ソリューションです。 これにより、エンティティ モデルを定義し、データベースに Database-First または Model-First を使って簡単にマップできるようになるため、クエリの記述をすぐに始めることができます。

[Web サイト](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer は ADO.NET Entity Framework、NHibernate、LinqConnect、Telerik Data Access、LINQ to SQL 用の強力な ORM デザイナーです。 Model-First および Database-First アプローチを使って ORM モデルを設計し、C# または Visual Basic .NET のコードを生成することができます。 ORM モデルをデザインするための新しいアプローチが導入されているため、生産性が向上し、データベース アプリケーションの開発が容易になります。

[Web サイト](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a>EF Core のパワー ツール

Visual Studio 2017+ 拡張機能。 既存のデータベースまたは SQL Server データベース プロジェクトから DbContext と POCO クラスをリバース エンジニアリングして、さまざまな方法で DbContext を視覚化し、調査することができます。

[GitHub の Wiki](https://github.com/ErikEJ/SqlCeToolbox/wiki/EF-Core-Power-Tools)

## <a name="extensions"></a>拡張機能

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

データの変更履歴の自動記録をサポートする、Microsoft.EntityFrameworkCore 用のプラグイン。

[GitHub リポジトリ](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a>Microsoft.EntityFrameworkCore.DynamicLinq

非同期サポートを追加する Microsoft.EntityFrameworkCore 用の動的 LINQ 拡張機能

 [GitHub リポジトリ](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efcorepractices"></a>EFCore.Practices

N+1 クエリをスキャンする小規模なフレームワークなど、API のテストをサポートする優れたプラクティスの一部が取得されます。

[GitHub リポジトリ](https://github.com/riezebosch/efcore-practices/tree/master/src/EFCore.Practices/)

### <a name="efsecondlevelcachecore"></a>EFSecondLevelCache.Core

第 2 レベルのキャッシュ ライブラリ。 第 2 レベルのキャッシュは、クエリ キャッシュです。 EF コマンドの結果はキャッシュに保存されます。これにより同じ EF コマンドが実行されたときにデータベースからではなくキャッシュからデータが取得されるようになります。

[GitHub リポジトリ](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="detachedentityframework"></a>Detached.EntityFramework

デタッチされたエンティティのグラフ (子エンティティとリストを含むエンティティ) 全体を読み込んで、保存します。 [GraphDiff](https://github.com/refactorthis/GraphDiff/) からインスパイアされています。 監査や改ページといった一部の反復的なタスクを簡略化するプラグインもいくつか追加されます。

[GitHub リポジトリ](https://github.com/leonardoporro/Detached/)

### <a name="entityframeworkcoreprimarykey"></a>EntityFrameworkCore.PrimaryKey

すべてのエンティティから (複合キーを含む) 主キーをディクショナリとして取得します。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcorerx"></a>EntityFrameworkCore.Rx

Entity Framework エンティティの Hot Observable の事後対応型拡張機能ラッパー。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.Rx/)

### <a name="entityframeworkcoretriggers"></a>EntityFrameworkCore.Triggers

挿入、更新、削除イベントを含むエンティティにトリガーを追加します。 それぞれに対して、前、後、障害発生時の 3 つのイベントがあります。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.Triggers/)

### <a name="entityframeworkcoretypedoriginalvalues"></a>EntityFrameworkCore.TypedOriginalValues

エンティティのプロパティの OriginalValue に型指定されたアクセスができるようになります。 単純または複合プロパティがサポートされますが、ナビゲーション/コレクションはサポートされません。

[GitHub リポジトリ](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a>Geco

Geco は複数形化/単数形化のサポートと、C# 6.0 の挿入文字列に基づき .Net Core で実行される編集可能なテンプレートを含むリバース モデル ジェネレーターを提供します。 また、SQL マージ スクリプトとスクリプト ランナーを含むシード スクリプト ジェネレーターも提供します。

[GitHub リポジトリ](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a>LinqKit.Microsoft.EntityFrameworkCore

LinqKit.Microsoft.EntityFrameworkCore は、LINQ to SQL と EntityFrameworkCore のパワー ユーザー向けの無料の拡張機能セットです。 Include(...) と IDbAsync をサポートします。

[GitHub リポジトリ](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq.EntityFrameworkCore は、.NET 関数の一部のサブセットのみをサポートするエンティティ フレームワークなどの LINQ プロバイダーの使用、関数の再利用、クエリの再記述と null セーフ設定、翻訳可能な述語とセレクターを使った動的クエリの構築に便利な拡張機能を提供します。

[GitHub リポジトリ](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

リポジトリ、作業パターン、分散トランザクション対応の複数のデータベースをサポートする Microsoft.EntityFrameworkCore のプラグイン。

[GitHub リポジトリ](https://github.com/Arch/UnitOfWork/)

### <a name="entityframeworklazyloading"></a>EntityFramework.LazyLoading

EF Core 1.1 の遅延読み込み

[GitHub リポジトリ](https://github.com/darxis/EntityFramework.LazyLoading)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

一括操作 (挿入、更新、削除) の EntityFrameworkCore 拡張機能。

[GitHub リポジトリ](https://github.com/borisdj/EFCore.BulkExtensions)
