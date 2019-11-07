---
title: 既定値-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: b6ac283d551e2c6ee119f7de6933363b5d8793a1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655905"
---
# <a name="default-values"></a><span data-ttu-id="c6a0b-102">既定値</span><span class="sxs-lookup"><span data-stu-id="c6a0b-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="c6a0b-103">このセクションの構成は、リレーショナル データベース全般に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c6a0b-104">ここに示す拡張方法は、リレーショナル データベース プロバイダーをインストールすると (共有 *Microsoft.EntityFrameworkCore.Relational* パッケージによって) 利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c6a0b-105">列の既定値は、新しい行が挿入されても列に値が指定されていない場合に挿入される値です。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="c6a0b-106">規約</span><span class="sxs-lookup"><span data-stu-id="c6a0b-106">Conventions</span></span>

<span data-ttu-id="c6a0b-107">慣例により、既定値は構成されていません。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c6a0b-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="c6a0b-108">Data Annotations</span></span>

<span data-ttu-id="c6a0b-109">データ注釈を使用して既定値を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c6a0b-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c6a0b-110">Fluent API</span></span>

<span data-ttu-id="c6a0b-111">Fluent API を使用して、プロパティの既定値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-111">You can use the Fluent API to specify the default value for a property.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValue.cs?name=DefaultValue&highlight=9)]

<span data-ttu-id="c6a0b-112">既定値の計算に使用される SQL フラグメントを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c6a0b-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValueSql.cs?name=DefaultValueSql&highlight=9)]
