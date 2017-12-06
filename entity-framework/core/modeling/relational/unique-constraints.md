---
title: "代替キー (一意制約) - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
ms.technology: entity-framework-core
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 1b7e2bef6ede95f8c27211ba00dcc6b97cccde9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="030ee-102">代替キー (一意制約)</span><span class="sxs-lookup"><span data-stu-id="030ee-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="030ee-103">このセクションの構成は、一般にリレーショナル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="030ee-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="030ee-104">ここで示すように拡張メソッドが使用可能になるリレーショナル データベース プロバイダーをインストールするときに (共有のため*Microsoft.EntityFrameworkCore.Relational*パッケージ)。</span><span class="sxs-lookup"><span data-stu-id="030ee-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="030ee-105">Unique 制約は、モデル内の代替キーごとに導入されました。</span><span class="sxs-lookup"><span data-stu-id="030ee-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="030ee-106">規則</span><span class="sxs-lookup"><span data-stu-id="030ee-106">Conventions</span></span>

<span data-ttu-id="030ee-107">慣例により、インデックスと代替キーの導入された制約の名前は`AK_<type name>_<property name>`します。</span><span class="sxs-lookup"><span data-stu-id="030ee-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="030ee-108">複合の代替キーの`<property name>`プロパティ名のアンダー スコアの区切られたリストになります。</span><span class="sxs-lookup"><span data-stu-id="030ee-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="030ee-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="030ee-109">Data Annotations</span></span>

<span data-ttu-id="030ee-110">Unique 制約は、データ注釈を使用していない構成できます。</span><span class="sxs-lookup"><span data-stu-id="030ee-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="030ee-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="030ee-111">Fluent API</span></span>

<span data-ttu-id="030ee-112">Fluent API を使用して、代替キーのインデックスと制約名を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="030ee-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
