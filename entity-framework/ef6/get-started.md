---
title: Entity Framework 6 - EF6 の概要
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
ms.openlocfilehash: c25bf16bd2c39530d54b286b7743ceb83c941e4d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489291"
---
# <a name="get-started-with-entity-framework-6"></a>Entity Framework 6 の概要

このガイドには、選択したドキュメント記事への一連のリンクと、すぐに使用を開始するためのチュートリアルとビデオが含まれています。

## <a name="fundamentals"></a>Fundamentals

* [Entity Framework を取得する](~/ef6/fundamentals/install.md)

  ここでは、お使いのアプリケーションに Entity Framework を追加し、EF デザイナーを使用する場合は、Visual Studio にそれを確実にインストールする方法を学習します。

* [モデルの作成: Code First、EF デザイナー、EF ワークフロー](~/ef6/modeling/index.md)

  お使いの EF モデルに、コードの記述と、ボックスや線の描画のどちらを指定したいですか?
EF を使用して、オブジェクトを既存のデータベースにマップしますか? または EF を使用して自分のオブジェクトに合わせたデータベースを作成したいですか?
ここでは、EF6 を使用する 2 つの異なるアプローチ、EF デザイナーと Code First について学習します。
説明に従って、違いに関するビデオをご覧ください。

* [DbContext の操作](~/ef6/fundamentals/working-with-dbcontext.md)

  DbContext は、使用方法を学習する必要のある最も重要な最初の EF 型です。 これは、データベース クエリのスタート パッドとして機能し、オブジェクトへの変更を追跡して、永続化してデータベースに戻せるようにします。

* [質問する](~/ef6/resources/get-help.md)

  専門家のサポートを受け、コミュニティに自分の回答を投稿する方法について説明しています。

* [寄稿](http://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 では、オープン開発モデルを使用します。 弊社の GitHub リポジトリにアクセスすることで、EF の機能向上にご協力いただける方法を紹介しています。

## <a name="code-first-resources"></a>Code First リソース

  - [既存のデータベース ワークフローへの Code First](~/ef6/modeling/code-first/workflows/existing-database.md)
  - [新しいデータベース ワークフローへの Code First](~/ef6/modeling/code-first/workflows/new-database.md)
  - [Code First を使用した列挙型のマッピング](~/ef6/modeling/code-first/data-types/enums.md)
  - [Code First を使用した空間型のマッピング](~/ef6/modeling/code-first/data-types/spatial.md)
  - [カスタム Code First 規約の記述](~/ef6/modeling/code-first/conventions/custom.md)
  - [Visual Basic での Code First の Fluent 構成の使用](~/ef6/modeling/code-first/fluent/vb.md)
  - [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)
  - [チーム環境での Code First Migrations](~/ef6/modeling/code-first/migrations/teams.md)
  - [自動の Code First Migrations](~/ef6/modeling/code-first/migrations/automatic.md) (これは推奨されなくなりました)

## <a name="ef-designer-resources"></a>EF Designer リソース
  - [Database First ワークフロー](~/ef6/modeling/designer/workflows/database-first.md)
  - [Model First ワークフロー](~/ef6/modeling/designer/workflows/model-first.md)
  - [列挙型のマッピング](~/ef6/modeling/designer/data-types/enums.md)
  - [空間型のマッピング](~/ef6/modeling/designer/data-types/spatial.md)
  - [Table-Per Hierarchy 継承のマッピング](~/ef6/modeling/designer/inheritance/tph.md)
  - [Table-Per Type 継承のマッピング](~/ef6/modeling/designer/inheritance/tpt.md)
  - [更新のためのストアド プロシージャ マッピング](~/ef6/modeling/designer/stored-procedures/cud.md)
  - [クエリのためのストアド プロシージャ マッピング](~/ef6/modeling/designer/stored-procedures/query.md)
  - [エンティティ分割](~/ef6/modeling/designer/entity-splitting.md)
  - [テーブル分割](~/ef6/modeling/designer/table-splitting.md)
  - [定義クエリ](~/ef6/modeling/designer/advanced/defining-query.md) (詳細)
  - [テーブル値関数](~/ef6/modeling/designer/advanced/tvfs.md) (詳細)

## <a name="other-resources"></a>その他のリソース
  - [非同期クエリと保存](~/ef6/fundamentals/async.md)
  - [WinForms とのデータバインド](~/ef6/fundamentals/databinding/winforms.md)
  - [WPF とのデータバインド](~/ef6/fundamentals/databinding/wpf.md)
  - [自己追跡エンティティを使用した接続解除シナリオ](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md) (これは推奨されなくなりました)
