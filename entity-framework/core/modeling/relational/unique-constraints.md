---
title: 代替キー (Unique 制約)-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 7afcb804aeeccbd5e07c228a8fd9850ca00a2919
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197607"
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="c57cf-102">代替キー (一意の制約)</span><span class="sxs-lookup"><span data-stu-id="c57cf-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="c57cf-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c57cf-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c57cf-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c57cf-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c57cf-105">一意の制約は、モデル内の各代替キーに対して導入されます。</span><span class="sxs-lookup"><span data-stu-id="c57cf-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="c57cf-106">規約</span><span class="sxs-lookup"><span data-stu-id="c57cf-106">Conventions</span></span>

<span data-ttu-id="c57cf-107">慣例により、代替キーに対して導入されたインデックスと制約に`AK_<type name>_<property name>`はという名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="c57cf-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="c57cf-108">複合キー `<property name>`の場合は、アンダースコアで区切られたプロパティ名のリストになります。</span><span class="sxs-lookup"><span data-stu-id="c57cf-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c57cf-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c57cf-109">Data Annotations</span></span>

<span data-ttu-id="c57cf-110">データ注釈を使用して Unique 制約を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="c57cf-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c57cf-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c57cf-111">Fluent API</span></span>

<span data-ttu-id="c57cf-112">Fluent API を使用して、代替キーのインデックスと制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c57cf-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
