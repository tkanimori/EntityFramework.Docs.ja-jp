---
title: シーケンス-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: b810caaffa329bb5ad6f3486145d0ade9287eada
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656115"
---
# <a name="sequences"></a>シーケンス

> [!NOTE]  
> このセクションの構成は、リレーショナル データベース全般に適用されます。 ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。

シーケンスでは、データベース内に連続する数値が生成されます。 シーケンスは、特定のテーブルに関連付けられていません。

## <a name="conventions"></a>規約

慣例により、シーケンスはモデルには導入されません。

## <a name="data-annotations"></a>データの注釈

データ注釈を使用してシーケンスを構成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用して、モデル内にシーケンスを作成できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Sequence.cs?name=Model&highlight=7)]

また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceConfigured.cs?name=Sequence&highlight=7,8,9)]

シーケンスが導入されたら、それを使用して、モデル内のプロパティの値を生成できます。 たとえば、[既定値](default-values.md)を使用して、シーケンスから次の値を挿入できます。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceUsed.cs?name=Default&highlight=13)]
