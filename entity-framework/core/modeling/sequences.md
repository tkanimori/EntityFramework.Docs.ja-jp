---
title: シーケンス-EF Core
description: Entity Framework Core モデルでのシーケンスの構成
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: 8848a7237934091c1253ec61a37eea1c4326d1cb
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071395"
---
# <a name="sequences"></a>シーケンス

> [!NOTE]  
> シーケンスは、通常、リレーショナルデータベースでのみサポートされる機能です。 Cosmos などの非リレーショナルデータベースを使用している場合は、一意の値の生成に関するデータベースドキュメントを確認してください。

シーケンスは、データベース内で一意の連続する数値を生成します。 シーケンスは特定のテーブルに関連付けられていません。また、複数のテーブルを設定して、同じシーケンスから値を描画することもできます。

## <a name="basic-usage"></a>基本的な使用方法

モデルでシーケンスを設定し、それを使用してプロパティの値を生成することができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

シーケンスから値を生成するために使用される特定の SQL はデータベース固有であることに注意してください。上の例は SQL Server で動作しますが、他のデータベースでは失敗します。 詳細については、特定のデータベースのドキュメントを参照してください。

## <a name="configuring-sequence-settings"></a>シーケンス設定の構成

また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
