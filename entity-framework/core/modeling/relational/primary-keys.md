---
title: 主キー-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656050"
---
# <a name="primary-keys"></a><span data-ttu-id="20773-102">主キー</span><span class="sxs-lookup"><span data-stu-id="20773-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="20773-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="20773-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="20773-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="20773-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="20773-105">主キー制約は、各エンティティ型のキーに対して導入されています。</span><span class="sxs-lookup"><span data-stu-id="20773-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="20773-106">規約</span><span class="sxs-lookup"><span data-stu-id="20773-106">Conventions</span></span>

<span data-ttu-id="20773-107">慣例として、データベースの主キーには `PK_<type name>`という名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="20773-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="20773-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="20773-108">Data Annotations</span></span>

<span data-ttu-id="20773-109">データ注釈を使用して、主キーのリレーショナルデータベース固有の側面を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="20773-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="20773-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="20773-110">Fluent API</span></span>

<span data-ttu-id="20773-111">Fluent API を使用して、データベース内の primary key 制約の名前を構成できます。</span><span class="sxs-lookup"><span data-stu-id="20773-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
