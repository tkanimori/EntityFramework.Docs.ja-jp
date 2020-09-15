---
title: Entity Framework 用語集-EF6
description: Entity Framework 6 用語集
author: divega
ms.date: 10/23/2016
uid: ef6/resources/glossary
ms.openlocfilehash: 819bdda133b427fa6f8fc1915cde07560e6ca568
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072890"
---
# <a name="entity-framework-glossary"></a>Entity Framework 用語集
## <a name="code-first"></a>Code First
コードを使用して Entity Framework モデルを作成する。 モデルでは、既存のデータベースまたは新しいデータベースを対象にすることができます。

## <a name="context"></a>Context
データベースとのセッションを表すクラス。これにより、データのクエリと保存を行うことができます。 コンテキストは、DbContext または ObjectContext クラスから派生します。

## <a name="convention-code-first"></a>規則 (Code First)
クラスからモデルの形状を推論するために Entity Framework 使用する規則。

## <a name="database-first"></a>Database First
EF デザイナーを使用して、既存のデータベースを対象とする Entity Framework モデルを作成します。

## <a name="eager-loading"></a>一括読み込み
ある種類のエンティティに対するクエリもクエリの一部として関連エンティティを読み込む、関連データを読み込むパターン。

## <a name="ef-designer"></a>EF デザイナー
ボックスと行を使用して Entity Framework モデルを作成できる Visual Studio のビジュアルデザイナー。

## <a name="entity"></a>エンティティ
顧客、製品、注文などのアプリケーション データを表すクラスまたはオブジェクト。

## <a name="entity-data-model"></a>エンティティ データ モデル
エンティティとその間のリレーションシップを記述するモデル。 EF では、EDM を使用して、開発者がプログラムを実行する概念モデルを記述します。 EDM は、Dr. Peter Chen によって導入されたエンティティリレーションシップモデルに基づいて構築されます。 EDM は、Microsoft の開発者およびサーバーテクノロジのスイート全体で共通のデータモデルになるという主な目標を使用して開発されました。 EDM は、OData プロトコルの一部としても使用されます。

## <a name="explicit-loading"></a>明示的読み込み
API を呼び出すことによって関連オブジェクトが読み込まれる場合に関連するデータを読み込むパターン。

## <a name="fluent-api"></a>Fluent API
Code First モデルを構成するために使用できる API。

## <a name="foreign-key-association"></a>外部キーの関連付け
外部キーを表すプロパティが依存エンティティのクラスに含まれるエンティティ間のアソシエーション。 たとえば、Product には CategoryId プロパティが含まれています。

## <a name="identifying-relationship"></a>リレーションシップの識別
プリンシパル エンティティの主キーが依存エンティティの主キーの一部であるリレーションシップ。 このようなリレーションシップでは、プリンシパル エンティティが存在しないと、依存エンティティは存在できません。

## <a name="independent-association"></a>独立関連付け
依存エンティティのクラスの外部キーを表すプロパティがないエンティティ間のアソシエーション。 たとえば、Product クラスには Category へのリレーションシップが含まれていますが、CategoryId プロパティはありません。 Entity Framework は、2つのアソシエーション end のエンティティの状態に関係なく、関連付けの状態を追跡します。

## <a name="lazy-loading"></a>遅延読み込み
ナビゲーションプロパティにアクセスしたときに関連オブジェクトが自動的に読み込まれる関連データの読み込みパターン。

## <a name="model-first"></a>Model First
EF デザイナーを使用して Entity Framework モデルを作成し、その後、新しいデータベースを作成するために使用します。

## <a name="navigation-property"></a>ナビゲーション プロパティ
別のエンティティを参照するエンティティのプロパティ。 たとえば、Product に Category ナビゲーションプロパティが含まれていて、Category に Products ナビゲーションプロパティが含まれているとします。

## <a name="poco"></a>POCO
Plain Old CLR Object の頭字語。 任意のフレームワークとの依存関係を持たない単純なユーザークラス。 EF のコンテキストでは、EntityObject から派生していないエンティティクラスは、任意のインターフェイスを実装するか、EF で定義されているすべての属性を保持します。 永続化フレームワークから切り離されたこのようなエンティティクラスは、"永続化に依存しない" とも呼ばれます。  

## <a name="relationship-inverse"></a>リレーションシップの逆
リレーションシップの反対側 (product など)。カテゴリとカテゴリ。梱包.

## <a name="self-tracking-entity"></a>自己追跡エンティティ
N 層の開発に役立つコード生成テンプレートから構築されたエンティティ。

## <a name="table-per-concrete-type-tpc"></a>具象型ごとのテーブル (TPC)
階層内の各非抽象型がデータベース内の別のテーブルにマップされる継承をマッピングする方法。

## <a name="table-per-hierarchy-tph"></a>階層ごとのテーブル (TPH)
階層内のすべての型がデータベース内の同じテーブルにマップされる継承をマッピングする方法。 識別子列は、各行が関連付けられている型を識別するために使用されます。

## <a name="table-per-type-tpt"></a>型ごとのテーブル (TPT)
階層内のすべての型の共通プロパティがデータベース内の同じテーブルにマップされる継承をマッピングする方法。ただし、それぞれの型に固有のプロパティは、別のテーブルにマップされます。

## <a name="type-discovery"></a>型の検出
Entity Framework モデルに含める必要がある型を識別するプロセス。
