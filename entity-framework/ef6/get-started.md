---
title: Entity Framework 6 - EF6 の概要
description: Entity Framework 6 の概要
author: divega
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: 825fe3716ade947f1660c8131100a1c3acba5a1d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072136"
---
# <a name="get-started-with-entity-framework-6"></a>Entity Framework 6 の概要

このガイドには、選択したドキュメント記事への一連のリンクと、すぐに使用を開始するためのチュートリアルとビデオが含まれています。

## <a name="fundamentals"></a>基礎

* [Entity Framework を取得する](xref:ef6/fundamentals/install)

  ここでは、お使いのアプリケーションに Entity Framework を追加し、EF デザイナーを使用する場合は、Visual Studio にそれを確実にインストールする方法を学習します。

* [モデルの作成: Code First、EF デザイナー、EF ワークフロー](xref:ef6/modeling/index)

  お使いの EF モデルに、コードの記述と、ボックスや線の描画のどちらを指定したいですか?
EF を使用して、オブジェクトを既存のデータベースにマップしますか? または EF を使用して自分のオブジェクトに合わせたデータベースを作成したいですか?
ここでは、EF6 を使用する次の 2 種類の方法について学習します: EF Designer と Code First。
説明に従って、違いに関するビデオをご覧ください。

* [DbContext の操作](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext は、使用方法を学習する必要のある最も重要な最初の EF 型です。 これは、データベース クエリのスタート パッドとして機能し、オブジェクトへの変更を追跡して、永続化してデータベースに戻せるようにします。

* [質問する](xref:ef6/resources/get-help)

  専門家のサポートを受け、コミュニティに自分の回答を投稿する方法について説明しています。

* [投稿](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 では、オープン開発モデルを使用します。 弊社の GitHub リポジトリにアクセスすることで、EF の機能向上にご協力いただける方法を紹介しています。

## <a name="code-first-resources"></a>Code First リソース

  - [既存のデータベース ワークフローへの Code First](xref:ef6/modeling/code-first/workflows/existing-database)
  - [新しいデータベース ワークフローへの Code First](xref:ef6/modeling/code-first/workflows/new-database)
  - [Code First を使用した列挙型のマッピング](xref:ef6/modeling/code-first/data-types/enums)
  - [Code First を使用した空間型のマッピング](xref:ef6/modeling/code-first/data-types/spatial)
  - [カスタム Code First 規約の記述](xref:ef6/modeling/code-first/conventions/custom)
  - [Visual Basic での Code First の Fluent 構成の使用](xref:ef6/modeling/code-first/fluent/vb)
  - [Code First Migrations](xref:ef6/modeling/code-first/migrations/index)
  - [チーム環境での Code First Migrations](xref:ef6/modeling/code-first/migrations/teams)
  - [自動の Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (これは推奨されなくなりました)

## <a name="ef-designer-resources"></a>EF Designer リソース
  - [Database First ワークフロー](xref:ef6/modeling/designer/workflows/database-first)
  - [Model First ワークフロー](xref:ef6/modeling/designer/workflows/model-first)
  - [列挙型のマッピング](xref:ef6/modeling/designer/data-types/enums)
  - [空間型のマッピング](xref:ef6/modeling/designer/data-types/spatial)
  - [Table-Per Hierarchy 継承のマッピング](xref:ef6/modeling/designer/inheritance/tph)
  - [Table-Per Type 継承のマッピング](xref:ef6/modeling/designer/inheritance/tpt)
  - [更新のためのストアド プロシージャ マッピング](xref:ef6/modeling/designer/stored-procedures/cud)
  - [クエリのためのストアド プロシージャ マッピング](xref:ef6/modeling/designer/stored-procedures/query)
  - [エンティティ分割](xref:ef6/modeling/designer/entity-splitting)
  - [テーブル分割](xref:ef6/modeling/designer/table-splitting)
  - [定義クエリ](xref:ef6/modeling/designer/advanced/defining-query) (詳細)
  - [テーブル値関数](xref:ef6/modeling/designer/advanced/tvfs) (詳細)

## <a name="other-resources"></a>その他のリソース
  - [非同期クエリと保存](xref:ef6/fundamentals/async)
  - [WinForms とのデータバインド](xref:ef6/fundamentals/databinding/winforms)
  - [WPF とのデータバインド](xref:ef6/fundamentals/databinding/wpf)
  - [自己追跡エンティティを使用した接続解除シナリオ](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (これは推奨されなくなりました)
