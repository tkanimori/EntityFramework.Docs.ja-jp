---
title: シーケンス-EF Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 6343e58dd79837cc69308a070c9814030505d7dd
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502439"
---
# <a name="sequences"></a><span data-ttu-id="22241-102">シーケンス</span><span class="sxs-lookup"><span data-stu-id="22241-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="22241-103">シーケンスは、通常、リレーショナルデータベースでのみサポートされる機能です。</span><span class="sxs-lookup"><span data-stu-id="22241-103">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="22241-104">Cosmos などの非リレーショナルデータベースを使用している場合は、一意の値の生成に関するデータベースドキュメントを確認してください。</span><span class="sxs-lookup"><span data-stu-id="22241-104">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="22241-105">シーケンスは、データベース内で一意の連続する数値を生成します。</span><span class="sxs-lookup"><span data-stu-id="22241-105">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="22241-106">シーケンスは特定のテーブルに関連付けられていません。また、複数のテーブルを設定して、同じシーケンスから値を描画することもできます。</span><span class="sxs-lookup"><span data-stu-id="22241-106">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="22241-107">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="22241-107">Basic usage</span></span>

<span data-ttu-id="22241-108">モデルでシーケンスを設定し、それを使用してプロパティの値を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="22241-108">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="22241-109">シーケンスから値を生成するために使用される特定の SQL はデータベース固有であることに注意してください。上の例は SQL Server で動作しますが、他のデータベースでは失敗します。</span><span class="sxs-lookup"><span data-stu-id="22241-109">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="22241-110">詳細については、特定のデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22241-110">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="22241-111">シーケンス設定の構成</span><span class="sxs-lookup"><span data-stu-id="22241-111">Configuring sequence settings</span></span>

<span data-ttu-id="22241-112">また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="22241-112">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
