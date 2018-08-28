---
title: 代替キー (一意制約) - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7ec58ee31aac79e15329dc8542f37fd117772fbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994192"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="a5197-102">代替キー (一意制約)</span><span class="sxs-lookup"><span data-stu-id="a5197-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="a5197-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="a5197-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="a5197-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a5197-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="a5197-105">Unique 制約には、モデル内の各代替キーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="a5197-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="a5197-106">規約</span><span class="sxs-lookup"><span data-stu-id="a5197-106">Conventions</span></span>

<span data-ttu-id="a5197-107">慣例により、インデックスと代替キー用に導入された制約の名前は`AK_<type name>_<property name>`します。</span><span class="sxs-lookup"><span data-stu-id="a5197-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="a5197-108">複合の代替キーの`<property name>`はプロパティ名、アンダー スコア区切りリストになります。</span><span class="sxs-lookup"><span data-stu-id="a5197-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a5197-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="a5197-109">Data Annotations</span></span>

<span data-ttu-id="a5197-110">Unique 制約は、データ注釈を使用していない構成できます。</span><span class="sxs-lookup"><span data-stu-id="a5197-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a5197-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a5197-111">Fluent API</span></span>

<span data-ttu-id="a5197-112">Fluent API を使用して、代替キーのインデックスと制約名を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="a5197-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
