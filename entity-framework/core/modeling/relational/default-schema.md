---
title: 既定のスキーマ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 1579fed007997aa4cf49b4c1290aee86c81c0000
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655968"
---
# <a name="default-schema"></a><span data-ttu-id="1ad1f-102">既定のスキーマ</span><span class="sxs-lookup"><span data-stu-id="1ad1f-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="1ad1f-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="1ad1f-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="1ad1f-105">既定のスキーマは、そのオブジェクトに対してスキーマが明示的に構成されていない場合にオブジェクトが作成されるデータベーススキーマです。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="1ad1f-106">規約</span><span class="sxs-lookup"><span data-stu-id="1ad1f-106">Conventions</span></span>

<span data-ttu-id="1ad1f-107">慣例により、データベースプロバイダーは最も適切な既定のスキーマを選択します。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="1ad1f-108">たとえば、Microsoft SQL Server では `dbo` スキーマが使用され、SQLite ではスキーマが使用されません (スキーマは SQLite ではサポートされていないため)。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="1ad1f-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="1ad1f-109">Data Annotations</span></span>

<span data-ttu-id="1ad1f-110">データ注釈を使用して既定のスキーマを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="1ad1f-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1ad1f-111">Fluent API</span></span>

<span data-ttu-id="1ad1f-112">Fluent API を使用して、既定のスキーマを指定できます。</span><span class="sxs-lookup"><span data-stu-id="1ad1f-112">You can use the Fluent API to specify a default schema.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultSchema.cs?name=DefaultSchema&highlight=7)]
