---
title: ユーザー定義関数のマッピング ‐ EF Core
description: データベース関数にユーザー定義関数をマッピングする
author: maumar
ms.date: 11/23/2020
uid: core/user-defined-function-mapping
ms.openlocfilehash: ba60abdc9c81b34b8f4ed8f501cf2f7e52ba9d7d
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657698"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="fa392-103">ユーザー定義関数のマッピング</span><span class="sxs-lookup"><span data-stu-id="fa392-103">User-defined function mapping</span></span>

<span data-ttu-id="fa392-104">EF Core では、クエリでユーザー定義の SQL 関数を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa392-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="fa392-105">このためには、モデルの構成時にその関数を CLR メソッドにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa392-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="fa392-106">LINQ クエリが SQL に変換されるとき、それがマップされている CLR 関数の代わりにユーザー定義関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="fa392-107">SQL 関数へのメソッドのマッピング</span><span class="sxs-lookup"><span data-stu-id="fa392-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="fa392-108">ユーザー定義関数のマッピングのしくみを説明するために、次のエンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="fa392-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="fa392-109">また、次のモデルを構成します。</span><span class="sxs-lookup"><span data-stu-id="fa392-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="fa392-110">ブログには多くの投稿を含めることができ、各投稿には多数のコメントが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="fa392-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="fa392-111">次に、ユーザー定義関数 `CommentedPostCountForBlog` を作成します。これにより、指定したブログのコメントが少なくとも 1 つある投稿の数が、ブログの `Id` に基づいて返されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

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

<span data-ttu-id="fa392-112">EF Core でこの関数を使用するために、ユーザー定義関数にマップする次の CLR メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="fa392-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="fa392-113">CLR メソッドの本体は重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="fa392-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="fa392-114">このメソッドは、EF Core がその引数を変換できない限り、クライアント側では呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="fa392-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="fa392-115">引数を変換できる場合は、EF Core ではメソッド シグネチャのみが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="fa392-116">このメソッドは、この例では `DbContext` に定義されていますが、他のクラスに静的メソッドとして定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="fa392-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="fa392-117">これで、この関数定義をモデル構成内のユーザー定義関数に関連付けることができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="fa392-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="fa392-118">EF Core は、既定で同じ名前のユーザー定義関数に CLR 関数をマップします。</span><span class="sxs-lookup"><span data-stu-id="fa392-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="fa392-119">名前が違う場合は、`HasName` を使用して、マップしたいユーザー定義関数に正しい名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="fa392-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="fa392-120">ここで次のクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="fa392-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="fa392-121">すると、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="fa392-122">カスタム SQL へのメソッドのマッピング</span><span class="sxs-lookup"><span data-stu-id="fa392-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="fa392-123">EF Core では、特定の SQL に変換されるユーザー定義関数も使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa392-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="fa392-124">この SQL 式は、ユーザー定義関数の構成時に `HasTranslation` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="fa392-125">次の例では、2 つの整数の差の割合を計算する関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="fa392-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="fa392-126">CLR メソッドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fa392-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="fa392-127">関数定義は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fa392-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="fa392-128">関数を定義したら、クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa392-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="fa392-129">EF Core は、データベース関数を呼び出す代わりに、HasTranslation から構築された SQL 式ツリーに基づいて、メソッドの本体を直接 SQL に変換します。</span><span class="sxs-lookup"><span data-stu-id="fa392-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="fa392-130">次の LINQ クエリでは、</span><span class="sxs-lookup"><span data-stu-id="fa392-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="fa392-131">これは次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="fa392-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="fa392-132">クエリ可能型関数のテーブル値関数へのマッピング</span><span class="sxs-lookup"><span data-stu-id="fa392-132">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="fa392-133">EF Core では、エンティティ型の `IQueryable` を返すユーザー定義 CLR メソッドを使用するテーブル値関数へのマッピングもサポートしています。これにより、EF Core で TVF をパラメーターにマップできるようになります。</span><span class="sxs-lookup"><span data-stu-id="fa392-133">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="fa392-134">この手順は、スカラー ユーザー定義関数を SQL 関数にマッピングするのと似ています。データベースの TVF、LINQ クエリで使用される CLR 関数、およびこの 2 つのマッピングが必要です。</span><span class="sxs-lookup"><span data-stu-id="fa392-134">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="fa392-135">例として、指定した "Like" しきい値を満たすコメントが少なくとも 1 つある投稿をすべて返すテーブル値関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="fa392-135">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

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

<span data-ttu-id="fa392-136">CLR メソッド シグネチャは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fa392-136">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="fa392-137">CLR 関数本体の `FromExpression` 呼び出しでは、通常の DbSet の代わりに関数を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa392-137">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="fa392-138">マッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fa392-138">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="fa392-139">[イシュー 23408](https://github.com/dotnet/efcore/issues/23408) が修正されるまで、DbSet のテーブルに対する既定のマッピングは、エンティティ型の `IQueryable` に対するマッピングにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="fa392-139">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="fa392-140">エンティティがキーなしでない場合など、必要に応じて、テーブルに対するマッピングを `ToTable` メソッドを使用して明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa392-140">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="fa392-141">クエリ可能型関数はテーブル値関数にマップする必要があり、`HasTranslation` を使用できません。</span><span class="sxs-lookup"><span data-stu-id="fa392-141">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="fa392-142">関数をマップすると、次のクエリでは、</span><span class="sxs-lookup"><span data-stu-id="fa392-142">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="fa392-143">次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fa392-143">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
