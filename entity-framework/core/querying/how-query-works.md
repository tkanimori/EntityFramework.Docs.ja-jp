---
title: クエリのしくみ - EF Core
description: Entity Framework Core 内部でのクエリのコンパイルおよび実行の方法に関する一般的な情報
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: 4f856bf7e084d020edee1585d5c6c64873a5ed15
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129006"
---
# <a name="how-queries-work"></a>クエリのしくみ

Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。 LINQ では C# (あるいは自分で選んだ .NET 言語) を使用し、派生コンテキストとエンティティ クラスに基づいて、厳密に型指定されたクエリを記述できます。

> [!NOTE]
> この記事は最新ではありません。クエリ パイプラインの設計で行われた変更に対応して、一部を更新する必要があります。 ここに記載されている何らかの動作について疑問がある場合は、[質問をお送りください](https://github.com/dotnet/efcore/issues/new/choose)。

## <a name="the-life-of-a-query"></a>クエリの全体像

次の説明は、各クエリが通過するプロセスの大まかな概要です。

1. LINQ クエリは、データベース プロバイダーで処理するために用意された表現をビルドするために、Entity Framework Core によって処理されます。
   1. クエリが実行されるたびにこの処理が実行される必要がないように、結果はキャッシュされます。
2. 結果が、データベース プロバイダーに渡されます。
   1. データベース プロバイダーは、クエリのどの部分がデータベースで評価できるかを識別します。
   2. クエリのこれらの部分は、データベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換されます。
   3. クエリがデータべースに送信され、結果セットが返されます (結果は、エンティティ インスタンスではなく、データベースからの値です)。
3. 結果セット内の各項目で、
   1. クエリが追跡クエリの場合は、EF では、データがコンテキスト インスタンスの変更トラッカーで既に存在するエンティティを表しているかどうかをチェックします。
      * 既にある場合、既存のエンティティが返されます。
      * 存在しない場合は、新しいエンティティが作成され、変更追跡が設定されて、新しいエンティティが返されます。
   2. クエリが非追跡クエリの場合は、常に新しいエンティティが作成され、返されます。

## <a name="when-queries-are-executed"></a>クエリの実行時

LINQ 演算子を呼び出すと、クエリのメモリ内表現が単純にビルドされます。 クエリは、結果が使用されるときにデータベースに送信されるだけです。

クエリでの結果がデータベースに送信される最も一般的な操作は、次のとおりです。

* `for` ループ内で結果を反復処理する
* `ToList`、`ToArray`、`Single`、`Count` または同等の非同期オーバーロードなどの演算子を使用する

> [!WARNING]
> **ユーザー入力を常に検証する:** クエリでパラメーターを使用し、リテラルをエスケープすることで、EF Core によって SQL インジェクション攻撃から保護されていますが、入力は検証されません。 信頼されていないソースの値が LINQ クエリで使用されたり、エンティティ プロパティに割り当てられたり、他の EF Core API に渡される前に、アプリケーションの要件ごとに適切な検証を実行する必要があります。 これには、動的にクエリを構築する際に使用されるユーザー入力も含まれます。 LINQ を使用している場合であっても、式をビルドするためのユーザー入力を許可しているなら、目的の式だけは構築できることを確認する必要があります。
