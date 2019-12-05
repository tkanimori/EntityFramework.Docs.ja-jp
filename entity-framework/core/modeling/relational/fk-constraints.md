---
title: Foreign Key 制約-EF Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: 2855137adf2ba3c9edaabd15a05f7a209f00f685
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824580"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="3fd31-102">外部キー制約</span><span class="sxs-lookup"><span data-stu-id="3fd31-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="3fd31-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3fd31-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="3fd31-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3fd31-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="3fd31-105">外部キー制約は、モデル内のリレーションシップごとに導入されます。</span><span class="sxs-lookup"><span data-stu-id="3fd31-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="3fd31-106">規約</span><span class="sxs-lookup"><span data-stu-id="3fd31-106">Conventions</span></span>

<span data-ttu-id="3fd31-107">規則により、外部キー制約には `FK_<dependent type name>_<principal type name>_<foreign key property name>`という名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="3fd31-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="3fd31-108">複合外部キーの場合 `<foreign key property name>` は、外部キープロパティ名のアンダースコア区切りの一覧になります。</span><span class="sxs-lookup"><span data-stu-id="3fd31-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="3fd31-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="3fd31-109">Data Annotations</span></span>

<span data-ttu-id="3fd31-110">外部キー制約の名前は、データ注釈を使用して構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="3fd31-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="3fd31-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="3fd31-111">Fluent API</span></span>

<span data-ttu-id="3fd31-112">Fluent API を使用して、リレーションシップの外部キー制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3fd31-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?name=Constraint&highlight=12)]
