---
title: ユーザー定義関数のマッピング ‐ EF Core
description: データベース関数にユーザー定義関数をマッピングする
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3e49ed9c49b38b98430128ffdc7ceef0b844b9df
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129123"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="7372c-103">ユーザー定義関数のマッピング</span><span class="sxs-lookup"><span data-stu-id="7372c-103">User-defined function mapping</span></span>

<span data-ttu-id="7372c-104">EF Core では、クエリでユーザー定義の SQL 関数を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7372c-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="7372c-105">このためには、モデルの構成時にその関数を CLR メソッドにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7372c-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="7372c-106">LINQ クエリが SQL に変換されるとき、それがマップされている CLR 関数の代わりにユーザー定義関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="7372c-107">SQL 関数へのメソッドのマッピング</span><span class="sxs-lookup"><span data-stu-id="7372c-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="7372c-108">ユーザー定義関数のマッピングのしくみを説明するために、次のエンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="7372c-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="7372c-109">また、次のモデルを構成します。</span><span class="sxs-lookup"><span data-stu-id="7372c-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="7372c-110">ブログには多くの投稿を含めることができ、各投稿には多数のコメントが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7372c-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="7372c-111">次に、ユーザー定義関数 `CommentedPostCountForBlog` を作成します。これにより、指定したブログのコメントが少なくとも 1 つある投稿の数が、ブログの `Id` に基づいて返されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="7372c-112">EF Core でこの関数を使用するために、ユーザー定義関数にマップする次の CLR メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7372c-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="7372c-113">CLR メソッドの本体は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="7372c-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="7372c-114">このメソッドは、EF Core がその引数を変換できない限り、クライアント側では呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="7372c-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="7372c-115">引数を変換できる場合は、EF Core ではメソッド シグネチャのみが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="7372c-116">このメソッドは、この例では `DbContext` に定義されていますが、他のクラスに静的メソッドとして定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="7372c-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="7372c-117">これで、この関数定義をモデル構成内のユーザー定義関数に関連付けることができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7372c-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="7372c-118">EF Core は、既定で同じ名前のユーザー定義関数に CLR 関数をマップします。</span><span class="sxs-lookup"><span data-stu-id="7372c-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="7372c-119">名前が違う場合は、`HasName` を使用して、マップしたいユーザー定義関数に正しい名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="7372c-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="7372c-120">ここで次のクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7372c-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="7372c-121">すると、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="7372c-122">カスタム SQL へのメソッドのマッピング</span><span class="sxs-lookup"><span data-stu-id="7372c-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="7372c-123">EF Core では、特定の SQL に変換されるユーザー定義関数も使用できます。</span><span class="sxs-lookup"><span data-stu-id="7372c-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="7372c-124">この SQL 式は、ユーザー定義関数の構成時に `HasTranslation` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="7372c-125">次の例では、2 つの整数の差の割合を計算する関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="7372c-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="7372c-126">CLR メソッドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7372c-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="7372c-127">関数定義は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7372c-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="7372c-128">関数を定義したら、クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7372c-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="7372c-129">EF Core は、データベース関数を呼び出す代わりに、HasTranslation から構築された SQL 式ツリーに基づいて、メソッドの本体を直接 SQL に変換します。</span><span class="sxs-lookup"><span data-stu-id="7372c-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="7372c-130">次の LINQ クエリでは、</span><span class="sxs-lookup"><span data-stu-id="7372c-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="7372c-131">これは次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="7372c-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a><span data-ttu-id="7372c-132">引数に基づいてユーザー定義関数の NULL 値の許容を構成する</span><span class="sxs-lookup"><span data-stu-id="7372c-132">Configuring nullability of user-defined function based on its arguments</span></span>

<span data-ttu-id="7372c-133">1 つ以上の引数が `null` の場合にのみ、ユーザー定義関数で `null` を返すことができる場合は、EF Core を使用すると、それを指定することができ、よりパフォーマンスが高い SQL が実現します。</span><span class="sxs-lookup"><span data-stu-id="7372c-133">If the user-defined function can only return `null` when one or more of its arguments are `null`, EFCore provides way to specify that, resulting in more performant SQL.</span></span> <span data-ttu-id="7372c-134">これを行うには、`PropagatesNullability()` 呼び出しを関連する関数パラメーター モデル構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="7372c-134">It can be done by adding a `PropagatesNullability()` call to the relevant function parameters model configuration.</span></span>

<span data-ttu-id="7372c-135">これを説明するために、ユーザー関数 `ConcatStrings` を定義します。</span><span class="sxs-lookup"><span data-stu-id="7372c-135">To illustrate this, define user function `ConcatStrings`:</span></span>

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

<span data-ttu-id="7372c-136">それにマップする 2 つの CLR メソッド:</span><span class="sxs-lookup"><span data-stu-id="7372c-136">and two CLR methods that map to it:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

<span data-ttu-id="7372c-137">モデル構成 (`OnModelCreating` メソッド内) は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7372c-137">The model configuration (inside `OnModelCreating` method) is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

<span data-ttu-id="7372c-138">最初の関数は、標準の方法で構成されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-138">The first function is configured in the standard way.</span></span> <span data-ttu-id="7372c-139">2 番目の関数は、NULL 値の許容の伝達の最適化を利用するように構成されており、関数が null パラメーターの近くでどのように動作するかについて詳しい情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="7372c-139">The second function is configured to take advantage of the nullability propagation optimization, providing more information on how the function behaves around null parameters.</span></span>

<span data-ttu-id="7372c-140">次のクエリを発行すると:</span><span class="sxs-lookup"><span data-stu-id="7372c-140">When issuing the following queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

<span data-ttu-id="7372c-141">次の SQL が得られます。</span><span class="sxs-lookup"><span data-stu-id="7372c-141">We get this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

<span data-ttu-id="7372c-142">2 番目のクエリでは、NULL 値の許容をテストするために関数自体を再評価する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7372c-142">The second query doesn't need to re-evaluate the function itself to test its nullability.</span></span>

> [!NOTE]
> <span data-ttu-id="7372c-143">この最適化は、パラメーターが `null` の場合にのみ関数で `null` を返すことができる場合にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="7372c-143">This optimization should only be used if the function can only return `null` when it's parameters are `null`.</span></span>

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="7372c-144">クエリ可能型関数のテーブル値関数へのマッピング</span><span class="sxs-lookup"><span data-stu-id="7372c-144">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="7372c-145">EF Core では、エンティティ型の `IQueryable` を返すユーザー定義 CLR メソッドを使用するテーブル値関数へのマッピングもサポートしています。これにより、EF Core で TVF をパラメーターにマップできるようになります。</span><span class="sxs-lookup"><span data-stu-id="7372c-145">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="7372c-146">この手順は、スカラー ユーザー定義関数を SQL 関数にマッピングするのと似ています。データベースの TVF、LINQ クエリで使用される CLR 関数、およびこの 2 つのマッピングが必要です。</span><span class="sxs-lookup"><span data-stu-id="7372c-146">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="7372c-147">例として、指定した "Like" しきい値を満たすコメントが少なくとも 1 つある投稿をすべて返すテーブル値関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="7372c-147">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="7372c-148">CLR メソッド シグネチャは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7372c-148">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="7372c-149">CLR 関数本体の `FromExpression` 呼び出しでは、通常の DbSet の代わりに関数を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7372c-149">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="7372c-150">マッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7372c-150">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="7372c-151">[イシュー 23408](https://github.com/dotnet/efcore/issues/23408) が修正されるまで、DbSet のテーブルに対する既定のマッピングは、エンティティ型の `IQueryable` に対するマッピングにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="7372c-151">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="7372c-152">エンティティがキーなしでない場合など、必要に応じて、テーブルに対するマッピングを `ToTable` メソッドを使用して明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7372c-152">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="7372c-153">クエリ可能型関数はテーブル値関数にマップする必要があり、`HasTranslation` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="7372c-153">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="7372c-154">関数をマップすると、次のクエリでは、</span><span class="sxs-lookup"><span data-stu-id="7372c-154">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="7372c-155">次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7372c-155">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
