---
title: EF Core 1.1 の新機能 - EF Core
author: divega
ms.author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 74c1033cab2704bdbb9fa4d3ce111df1f1c29418
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26049675"
---
# <a name="new-features-in-ef-core-11"></a>EF Core 1.1 の新機能

## <a name="modelling"></a>モデリング
### <a name="field-mapping"></a>フィールド マッピング
プロパティのバッキング フィールドを構成できます。 これは読み取り専用プロパティや、プロパティではなく Get/Set メソッドを持つデータで役立ちます。
### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>SQL Server でのメモリ最適化テーブルへのマッピング
エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。 EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。

## <a name="change-tracking"></a>Change tracking
### <a name="additional-change-tracking-apis-from-ef6"></a>EF6 から追加された変更追跡 API
`Reload`、`GetModifiedProperties`、`GetDatabaseValues` などです。

## <a name="query"></a>クエリ
### <a name="explicit-loading"></a>明示的な読み込み
以前にデータベースから読み込まれたエンティティのナビゲーション プロパティの作成をトリガーできます。
### <a name="dbsetfind"></a>DbSet.Find
主キーの値に基づいてエンティティをフェッチする簡単な方法を提供します。

## <a name="other"></a>その他
### <a name="connection-resiliency"></a>接続の復元性
失敗したデータベース コマンドを自動的に再試行します。 これは、一時的なエラーが発生しやすい SQL Azure への接続で特に有用です。
### <a name="simplified-service-replacement"></a>サービスの置き換えの簡略化
EF で使用される内部サービスの置き換えが簡単になります。
