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
# <a name="user-defined-function-mapping"></a>ユーザー定義関数のマッピング

EF Core では、クエリでユーザー定義の SQL 関数を使用できます。 このためには、モデルの構成時にその関数を CLR メソッドにマップする必要があります。 LINQ クエリが SQL に変換されるとき、それがマップされている CLR 関数の代わりにユーザー定義関数が呼び出されます。

## <a name="mapping-a-method-to-a-sql-function"></a>SQL 関数へのメソッドのマッピング

ユーザー定義関数のマッピングのしくみを説明するために、次のエンティティを定義します。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

また、次のモデルを構成します。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

ブログには多くの投稿を含めることができ、各投稿には多数のコメントが含まれる場合があります。

次に、ユーザー定義関数 `CommentedPostCountForBlog` を作成します。これにより、指定したブログのコメントが少なくとも 1 つある投稿の数が、ブログの `Id` に基づいて返されます。

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

EF Core でこの関数を使用するために、ユーザー定義関数にマップする次の CLR メソッドを定義します。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

CLR メソッドの本体は重要ではありません。 このメソッドは、EF Core がその引数を変換できない限り、クライアント側では呼び出されません。 引数を変換できる場合は、EF Core ではメソッド シグネチャのみが考慮されます。

> [!NOTE]
> このメソッドは、この例では `DbContext` に定義されていますが、他のクラスに静的メソッドとして定義することもできます。

これで、この関数定義をモデル構成内のユーザー定義関数に関連付けることができるようになりました。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

EF Core は、既定で同じ名前のユーザー定義関数に CLR 関数をマップします。 名前が違う場合は、`HasName` を使用して、マップしたいユーザー定義関数に正しい名前を指定します。

ここで次のクエリを実行します。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

すると、次の SQL が生成されます。

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>カスタム SQL へのメソッドのマッピング

EF Core では、特定の SQL に変換されるユーザー定義関数も使用できます。 この SQL 式は、ユーザー定義関数の構成時に `HasTranslation` メソッドを使用して指定されます。

次の例では、2 つの整数の差の割合を計算する関数を作成します。

CLR メソッドは次のとおりです。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

関数定義は次のとおりです。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

関数を定義したら、クエリで使用できます。 EF Core は、データベース関数を呼び出す代わりに、HasTranslation から構築された SQL 式ツリーに基づいて、メソッドの本体を直接 SQL に変換します。 次の LINQ クエリでは、

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

これは次の SQL を生成します。

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a>引数に基づいてユーザー定義関数の NULL 値の許容を構成する

1 つ以上の引数が `null` の場合にのみ、ユーザー定義関数で `null` を返すことができる場合は、EF Core を使用すると、それを指定することができ、よりパフォーマンスが高い SQL が実現します。 これを行うには、`PropagatesNullability()` 呼び出しを関連する関数パラメーター モデル構成に追加します。

これを説明するために、ユーザー関数 `ConcatStrings` を定義します。

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

それにマップする 2 つの CLR メソッド:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

モデル構成 (`OnModelCreating` メソッド内) は次のとおりです。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

最初の関数は、標準の方法で構成されます。 2 番目の関数は、NULL 値の許容の伝達の最適化を利用するように構成されており、関数が null パラメーターの近くでどのように動作するかについて詳しい情報を提供します。

次のクエリを発行すると:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

次の SQL が得られます。

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

2 番目のクエリでは、NULL 値の許容をテストするために関数自体を再評価する必要はありません。

> [!NOTE]
> この最適化は、パラメーターが `null` の場合にのみ関数で `null` を返すことができる場合にのみ使用してください。

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>クエリ可能型関数のテーブル値関数へのマッピング

EF Core では、エンティティ型の `IQueryable` を返すユーザー定義 CLR メソッドを使用するテーブル値関数へのマッピングもサポートしています。これにより、EF Core で TVF をパラメーターにマップできるようになります。 この手順は、スカラー ユーザー定義関数を SQL 関数にマッピングするのと似ています。データベースの TVF、LINQ クエリで使用される CLR 関数、およびこの 2 つのマッピングが必要です。

例として、指定した "Like" しきい値を満たすコメントが少なくとも 1 つある投稿をすべて返すテーブル値関数を使用します。

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

CLR メソッド シグネチャは次のとおりです。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> CLR 関数本体の `FromExpression` 呼び出しでは、通常の DbSet の代わりに関数を使用できます。

マッピングは次のとおりです。

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> [イシュー 23408](https://github.com/dotnet/efcore/issues/23408) が修正されるまで、DbSet のテーブルに対する既定のマッピングは、エンティティ型の `IQueryable` に対するマッピングにオーバーライドされます。 エンティティがキーなしでない場合など、必要に応じて、テーブルに対するマッピングを `ToTable` メソッドを使用して明示的に指定する必要があります。

> [!NOTE]
> クエリ可能型関数はテーブル値関数にマップする必要があり、`HasTranslation` を使用できません。

関数をマップすると、次のクエリでは、

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

次が生成されます。

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
