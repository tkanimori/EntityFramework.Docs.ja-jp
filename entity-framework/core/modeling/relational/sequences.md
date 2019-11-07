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
# <a name="sequences"></a><span data-ttu-id="e4d39-102">シーケンス</span><span class="sxs-lookup"><span data-stu-id="e4d39-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="e4d39-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="e4d39-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e4d39-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="e4d39-105">シーケンスでは、データベース内に連続する数値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="e4d39-106">シーケンスは、特定のテーブルに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="e4d39-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="e4d39-107">規約</span><span class="sxs-lookup"><span data-stu-id="e4d39-107">Conventions</span></span>

<span data-ttu-id="e4d39-108">慣例により、シーケンスはモデルには導入されません。</span><span class="sxs-lookup"><span data-stu-id="e4d39-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="e4d39-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="e4d39-109">Data Annotations</span></span>

<span data-ttu-id="e4d39-110">データ注釈を使用してシーケンスを構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="e4d39-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="e4d39-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="e4d39-111">Fluent API</span></span>

<span data-ttu-id="e4d39-112">Fluent API を使用して、モデル内にシーケンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-112">You can use the Fluent API to create a sequence in the model.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Sequence.cs?name=Model&highlight=7)]

<span data-ttu-id="e4d39-113">また、スキーマ、開始値、増分など、シーケンスの追加の側面を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceConfigured.cs?name=Sequence&highlight=7,8,9)]

<span data-ttu-id="e4d39-114">シーケンスが導入されたら、それを使用して、モデル内のプロパティの値を生成できます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="e4d39-115">たとえば、[既定値](default-values.md)を使用して、シーケンスから次の値を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="e4d39-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceUsed.cs?name=Default&highlight=13)]
