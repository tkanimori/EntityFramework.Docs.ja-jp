---
title: シーケンス-EF Core
description: Entity Framework Core モデルでのシーケンスの構成
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: e388bde11b1251564b37227e8884d2b8c34dc02e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128525"
---
# <a name="sequences"></a><span data-ttu-id="5d468-103">シーケンス</span><span class="sxs-lookup"><span data-stu-id="5d468-103">Sequences</span></span>

> [!NOTE]
> <span data-ttu-id="5d468-104">シーケンスは、通常、リレーショナルデータベースでのみサポートされる機能です。</span><span class="sxs-lookup"><span data-stu-id="5d468-104">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="5d468-105">Cosmos などの非リレーショナルデータベースを使用している場合は、一意の値の生成に関するデータベースドキュメントを確認してください。</span><span class="sxs-lookup"><span data-stu-id="5d468-105">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="5d468-106">シーケンスは、データベース内で一意の連続する数値を生成します。</span><span class="sxs-lookup"><span data-stu-id="5d468-106">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="5d468-107">シーケンスは特定のテーブルに関連付けられていません。また、複数のテーブルを設定して、同じシーケンスから値を描画することもできます。</span><span class="sxs-lookup"><span data-stu-id="5d468-107">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="5d468-108">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="5d468-108">Basic usage</span></span>

<span data-ttu-id="5d468-109">モデルでシーケンスを設定し、それを使用してプロパティの値を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="5d468-109">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="5d468-110">シーケンスから値を生成するために使用される特定の SQL はデータベース固有であることに注意してください。上の例は SQL Server で動作しますが、他のデータベースでは失敗します。</span><span class="sxs-lookup"><span data-stu-id="5d468-110">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="5d468-111">詳細については、特定のデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5d468-111">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="5d468-112">シーケンス設定の構成</span><span class="sxs-lookup"><span data-stu-id="5d468-112">Configuring sequence settings</span></span>

<span data-ttu-id="5d468-113">また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="5d468-113">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
