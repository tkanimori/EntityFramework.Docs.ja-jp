---
title: Entity Framework の用語集 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
ms.openlocfilehash: 9ed7a2d841c625de35de57edb4e57e69b89a3db9
ms.sourcegitcommit: 5d74ac575f813110db6d870720f50dd7606446bc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48881268"
---
# <a name="entity-framework-glossary"></a>Entity Framework の用語集
## <a name="code-first"></a>Code First
コードを使用して Entity Framework モデルを作成します。 モデルを対象にでき、既存のデータベースまたは新しいデータベース。

## <a name="context"></a>コンテキスト
クエリを実行し、データを保存することができます、データベースとのセッションを表すクラス。 コンテキストは、DbContext または ObjectContext クラスから派生します。

## <a name="convention-code-first"></a>規則 (1 つ目のコード)
Entity Framework を使用して、クラスからのモデルの形状を推測するルール。

## <a name="database-first"></a>最初のデータベースします。
既存のデータベースを対象とする EF デザイナーを使用して、Entity Framework モデルを作成します。

## <a name="eager-loading"></a>一括読み込み
1 つの型のエンティティのクエリ関連エンティティも読み込むクエリの一部として関連のデータの読み込みのパターン。

## <a name="ef-designer"></a>EF デザイナー
ボックスや線を使用して Entity Framework モデルを作成するための Visual Studio でのビジュアル デザイナーです。

## <a name="entity"></a>エンティティ
顧客、製品、注文などのアプリケーション データを表すクラスまたはオブジェクト。

## <a name="entity-data-model"></a>エンティティ データ モデル
エンティティおよびそれらの関係を記述するモデル。 EF では、EDM を使用して、対象となる概念モデルを記述する開発者プログラム。 EDM は、Dr で導入されたエンティティ リレーションシップ モデルに基づいています。Peter Chen します。 EDM は、もともとマイクロソフトの開発者およびサーバー テクノロジのスイート全体で common data model になることの主な目的に開発されました。 EDM は、OData プロトコルの一部としても使用されます。

## <a name="explicit-loading"></a>明示的読み込み
関連オブジェクトが、API を呼び出すことによって読み込まれる関連のデータの読み込みのパターン。

## <a name="fluent-api"></a>Fluent API
Code First モデルを構成するために使用できる API。

## <a name="foreign-key-association"></a>外部キーの関連付け
依存エンティティのクラスで、外部キーを表すプロパティが含まれている、エンティティ間の関連付け。 たとえば、製品には、CategoryId プロパティが含まれています。

## <a name="identifying-relationship"></a>リレーションシップを識別します。
プリンシパル エンティティの主キーが依存エンティティの主キーの一部であるリレーションシップ。 このようなリレーションシップでは、プリンシパル エンティティが存在しないと、依存エンティティは存在できません。

## <a name="independent-association"></a>独立アソシエーション
エンティティ間のアソシエーションの依存エンティティ クラスの外部キーを表すプロパティが存在しません。 たとえば、Product クラスには、カテゴリが CategoryId プロパティへのリレーションシップが含まれています。 Entity Framework では、2 つのアソシエーション end のエンティティの状態とは無関係にアソシエーションの状態を追跡します。

## <a name="lazy-loading"></a>遅延読み込み
ナビゲーション プロパティのアクセス時に関連するオブジェクトが読み込ま自動的に関連データの読み込みのパターン。

## <a name="model-first"></a>まずモデルします。
EF デザイナーを使用して、Entity Framework モデルを作成しに使用される新しいデータベースを作成します。

## <a name="navigation-property"></a>ナビゲーション プロパティ
別のエンティティを参照するエンティティのプロパティ。 たとえば、製品にはカテゴリのナビゲーション プロパティが含まれていて、カテゴリには、製品のナビゲーション プロパティが含まれています。

## <a name="poco"></a>POCO
Plain-old CLR オブジェクトの頭字語です。 任意のフレームワークでの依存関係がないシンプルなユーザー クラスです。 で、EF EntityObject から派生していない、任意のインターフェイスを実装または EF で定義されているすべての属性を格納するエンティティ クラスのコンテキスト。 「永続化に依存しない」に、永続化フレームワークから分離するこのようなエンティティ クラスともいいます。  

## <a name="relationship-inverse"></a>リレーションシップの逆関数
たとえば、製品、リレーションシップの反対側の端。カテゴリとカテゴリ。製品です。

## <a name="self-tracking-entity"></a>自己追跡エンティティ
N 層開発を支援するコード生成テンプレートから構築されるエンティティ。

## <a name="table-per-concrete-type-tpc"></a>テーブル-ごとの具象型 (TPC)
階層内の各非抽象型が別のデータベース内のテーブルに割り当てられている継承のマッピング方法です。

## <a name="table-per-hierarchy-tph"></a>-Table-per-hierarchy (TPH)
階層内のすべての型が、データベースの同じテーブルに割り当てられている継承のマッピング方法です。 列が行ごとの種類を識別するために使用される識別子が関連付けられています。

## <a name="table-per-type-tpt"></a>テーブルあたり型 (TPT)
階層内のすべての型の共通プロパティは、データベースで同じテーブルにマップされますが、各種類に固有のプロパティを別のテーブルにマップする継承のマッピング方法です。

## <a name="type-discovery"></a>型の探索
Entity Framework モデルの一部となる型を識別するプロセス。
