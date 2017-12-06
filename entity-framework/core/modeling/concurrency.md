---
title: "同時実行トークン - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
ms.technology: entity-framework-core
uid: core/modeling/concurrency
ms.openlocfilehash: 6574a9098d38c4aa525ffb4896adb01082420b5f
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="concurrency-tokens"></a><span data-ttu-id="4f439-102">同時実行制御トークン</span><span class="sxs-lookup"><span data-stu-id="4f439-102">Concurrency Tokens</span></span>

<span data-ttu-id="4f439-103">プロパティが、同時実行トークンとして構成されている場合 EF は、その他のユーザーが変更なし、データベース内でその値そのレコードに変更を保存するときにでチェックされます。</span><span class="sxs-lookup"><span data-stu-id="4f439-103">If a property is configured as a concurrency token then EF will check that no other user has modified that value in the database when saving changes to that record.</span></span> <span data-ttu-id="4f439-104">EF オプティミスティック同時実行パターンを使用して、つまりは値が変更されていないと想定して、データの保存が、値が変更されたが見つかった場合にスローしようとしています。 します。</span><span class="sxs-lookup"><span data-stu-id="4f439-104">EF uses an optimistic concurrency pattern, meaning it will assume the value has not changed and try to save the data, but throw if it finds the value has been changed.</span></span>

<span data-ttu-id="4f439-105">構成することがありますたとえば`LastName`で`Person`同時実行トークンであります。</span><span class="sxs-lookup"><span data-stu-id="4f439-105">For example we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="4f439-106">つまり、1 人のユーザーは、いくつかの変更を保存しようとした場合、 `Person`、別のユーザーが変更されて、`LastName`し、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="4f439-106">This means that if one user tries to save some changes to a `Person`, but another user has changed the `LastName` then an exception will be thrown.</span></span> <span data-ttu-id="4f439-107">アプリケーションがこのレコードが、変更を保存する前にも同じの実際のユーザーを表すことを確認するユーザーの入力を求めるように必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f439-107">This may be desirable so that your application can prompt the user to ensure this record still represents the same actual person before saving their changes.</span></span>

> [!NOTE]
> <span data-ttu-id="4f439-108">このページは、同時実行トークンを構成する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="4f439-108">This page documents how to configure concurrency tokens.</span></span> <span data-ttu-id="4f439-109">参照してください[同時実行の処理](../saving/concurrency.md)アプリケーションでオプティミスティック同時実行制御を使用する方法の例についてはします。</span><span class="sxs-lookup"><span data-stu-id="4f439-109">See [Handling Concurrency](../saving/concurrency.md) for examples of how to use optimistic concurrency in your application.</span></span>

## <a name="how-concurrency-tokens-work-in-ef"></a><span data-ttu-id="4f439-110">EF での同時実行トークンのしくみ</span><span class="sxs-lookup"><span data-stu-id="4f439-110">How concurrency tokens work in EF</span></span>

<span data-ttu-id="4f439-111">データ ストアは、同時実行トークンを更新またはまだ削除されているすべてのレコードが最初、コンテキストはデータベースからデータを読み込むときに割り当てられた同時実行トークンに同じ値を持つことを確認して適用できます。</span><span class="sxs-lookup"><span data-stu-id="4f439-111">Data stores can enforce concurrency tokens by checking that any record being updated or deleted still has the same value for the concurrency token that was assigned when the context originally loaded the data from the database.</span></span>

<span data-ttu-id="4f439-112">たとえば、リレーショナル データベースこれを実現するで同時実行トークンを含めることによって、`WHERE`いずれかの句`UPDATE`または`DELETE`コマンドとその影響を受けた行の番号を確認します。</span><span class="sxs-lookup"><span data-stu-id="4f439-112">For example, relational databases achieve this by including the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` commands and checking the number of rows that were affected.</span></span> <span data-ttu-id="4f439-113">同時実行トークンがまだと一致する場合は、1 つの行が更新されます。</span><span class="sxs-lookup"><span data-stu-id="4f439-113">If the concurrency token still matches then one row will be updated.</span></span> <span data-ttu-id="4f439-114">データベース内の値が変更された場合、行は更新されません。</span><span class="sxs-lookup"><span data-stu-id="4f439-114">If the value in the database has changed, then no rows are updated.</span></span>

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="conventions"></a><span data-ttu-id="4f439-115">規則</span><span class="sxs-lookup"><span data-stu-id="4f439-115">Conventions</span></span>

<span data-ttu-id="4f439-116">規則では、プロパティは同時実行トークンとして構成ことはありません。</span><span class="sxs-lookup"><span data-stu-id="4f439-116">By convention, properties are never configured as concurrency tokens.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4f439-117">データの注釈</span><span class="sxs-lookup"><span data-stu-id="4f439-117">Data Annotations</span></span>

<span data-ttu-id="4f439-118">同時実行トークンとしてプロパティを構成するのには、データ注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f439-118">You can use the Data Annotations to configure a property as a concurrency token.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a><span data-ttu-id="4f439-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="4f439-119">Fluent API</span></span>

<span data-ttu-id="4f439-120">Fluent API を使用して、同時実行トークンとしてプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="4f439-120">You can use the Fluent API to configure a property as a concurrency token.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a><span data-ttu-id="4f439-121">タイムスタンプまたは行のバージョン</span><span class="sxs-lookup"><span data-stu-id="4f439-121">Timestamp/row version</span></span>

<span data-ttu-id="4f439-122">タイムスタンプは、新しい値がデータベースによって生成された行が挿入または更新するたびに場所プロパティです。</span><span class="sxs-lookup"><span data-stu-id="4f439-122">A timestamp is a property where a new value is generated by the database every time a row is inserted or updated.</span></span> <span data-ttu-id="4f439-123">プロパティは、同時実行トークンとしても扱われます。</span><span class="sxs-lookup"><span data-stu-id="4f439-123">The property is also treated as a concurrency token.</span></span> <span data-ttu-id="4f439-124">これにより、その他のユーザー データのクエリを実行するために更新しようとしている行を変更した場合、例外が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4f439-124">This ensures you will get an exception if anyone else has modified a row that you are trying to update since you queried for the data.</span></span>

<span data-ttu-id="4f439-125">これを実現する方法については、使用中のデータベース プロバイダーによって決まります。</span><span class="sxs-lookup"><span data-stu-id="4f439-125">How this is achieved is up to the database provider being used.</span></span> <span data-ttu-id="4f439-126">SQL Server のタイムスタンプは、通常で使用される、 *byte[]*となるプロパティの設定として、 *ROWVERSION*データベース内の列です。</span><span class="sxs-lookup"><span data-stu-id="4f439-126">For SQL Server, timestamp is usually used on a *byte[]* property, which will be setup as a *ROWVERSION* column in the database.</span></span>

### <a name="conventions"></a><span data-ttu-id="4f439-127">規則</span><span class="sxs-lookup"><span data-stu-id="4f439-127">Conventions</span></span>

<span data-ttu-id="4f439-128">規則では、プロパティはタイムスタンプとして構成されません。</span><span class="sxs-lookup"><span data-stu-id="4f439-128">By convention, properties are never configured as timestamps.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="4f439-129">データの注釈</span><span class="sxs-lookup"><span data-stu-id="4f439-129">Data Annotations</span></span>

<span data-ttu-id="4f439-130">データ注釈を使用して、タイムスタンプとしてプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="4f439-130">You can use Data Annotations to configure a property as a timestamp.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a><span data-ttu-id="4f439-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="4f439-131">Fluent API</span></span>

<span data-ttu-id="4f439-132">Fluent API を使用して、タイムスタンプとしてプロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="4f439-132">You can use the Fluent API to configure a property as a timestamp.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Timestamp.cs#ConfigureTimestampFluent)]
