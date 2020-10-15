---
title: 関連データの一括読み込み - EF Core
description: Entity Framework Core による関連データの一括読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062577"
---
# <a name="eager-loading-of-related-data"></a>関連データの一括読み込み

## <a name="eager-loading"></a>一括読み込み

`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。 次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。

複数のリレーションシップの関連データを 1 つのクエリに含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>複数のレベルを含める

`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。 次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

これらのすべての呼び出しを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。 たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。 両方を含めるには、ルートから始まる各インクルード パスを指定する必要があります。 たとえば、`Blog -> Posts -> Author` や `Blog -> Posts -> Tags`す。 これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は組み合わされます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>単一クエリと分割クエリ

### <a name="single-queries"></a>単一のクエリ

リレーショナル データベースでは、JOIN を導入するとすべての関連エンティティが既定で読み込まれます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製され、いわゆる "デカルト爆発" の問題が発生します。 さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。 既定で EF Core では、パフォーマンスの問題の原因となる可能性があるコレクション インクルードを読み込んでいるクエリが検出されると、警告が出力されます。

### <a name="split-queries"></a>分割クエリ

> [!NOTE]
> この機能は EF Core 5.0 で導入されています。

EF では、特定の LINQ クエリを複数の SQL クエリに "*分割*" するように指定できます。 JOIN の代わりに、分割クエリでは、含まれている一対多のナビゲーションごとに追加の SQL クエリが実行されます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

これにより、次の SQL が生成されます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> 一対一の関連エンティティは、常に同じクエリ内で JOIN によって読み込まれます。パフォーマンス上の影響がないためです。

### <a name="enabling-split-queries-globally"></a>分割クエリをグローバルに有効にする

分割クエリは、アプリケーションのコンテキストで既定値として構成することもできます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

分割クエリが既定値として構成されている場合でも、特定のクエリを単一のクエリとして実行するように構成することができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

クエリ分割モードが明示的に指定されておらず (グローバルでも、クエリ上でも)、単一のクエリによって複数のコレクション インクルードが読み込まれることが EF Core で検出された場合は、結果として生じる可能性のあるパフォーマンスの問題に注意するよう警告が出力されます。 クエリ モードを SingleQuery に設定すると、警告は生成されません。

### <a name="characteristics-of-split-queries"></a>分割クエリの特性

分割クエリによって JOIN とデカルト爆発に関連するパフォーマンス上の問題が回避されますが、いくつかの欠点もあります。

* ほとんどのデータベースでは単一クエリに対してデータの整合性が保証されますが、複数クエリに対してこのような保証は存在しません。 クエリの実行と同時にデータベースが更新された場合、生成されるデータの整合性が失われる可能性があります。 これはシリアル化可能なトランザクションまたはスナップショット トランザクションでクエリをラップすることで軽減できますが、これにより、それ自体のパフォーマンス上の問題が発生する可能性があります。 詳細については、ご利用のデータベースのドキュメントを参照してください。
* 現在、各クエリは、データベースに対する追加のネットワーク ラウンドトリップを意味します。 特にデータベースの待機時間が長い場合 (クラウド サービスなど)、複数のネットワーク ラウンドトリップによってパフォーマンスが低下する可能性があります。
* 一部のデータベースでは、複数のクエリの結果を同時に使用することが許可されていますが (SQL Server と MARS、Sqlite)、ほとんどの場合、特定の時点でアクティブにできるクエリは 1 つだけです。 そのため、後のクエリを実行する前に、前のクエリの結果をすべてアプリケーションのメモリにバッファーする必要があります。これにより、メモリ要件が大きくなります。

残念ながら、すべてのシナリオに適合する関連エンティティの読み込み方法はありません。 単一クエリと分割クエリの長所と短所を慎重に検討し、ご自身のニーズに合うものを選択してください。

## <a name="filtered-include"></a>フィルター処理されたインクルード

> [!NOTE]
> この機能は EF Core 5.0 で導入されています。

インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を適用できます。これにより、結果のフィルター処理と並べ替えが可能になります。

サポートされている操作は、`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip`、`Take` です。

このような操作は、次の例に示すように、インクルード メソッドに渡されるラムダのコレクション ナビゲーションに適用する必要があります。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

インクルードされている各ナビゲーションでは、フィルター操作の一意のセットが 1 つだけ許可されます。 特定のコレクション ナビゲーション (次の例では `blog.Posts`) に複数のインクルード操作が適用されている場合、フィルター操作はそのいずれかでのみ指定できます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

代わりに、複数回インクルードされているナビゲーションごとに、同一の操作を適用することもできます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> クエリの追跡では、[ナビゲーション修正](xref:core/querying/tracking)により、フィルター処理されたインクルードの結果が予期しないものになることがあります。 以前にクエリされ、変更トラッカーに格納されていた関連エンティティはすべて、フィルターの要件を満たしていなくても、フィルター処理されたインクルードのクエリの結果に表示されます。 そのような状況でフィルター処理されたインクルードを使用する場合は、`NoTracking` クエリを使用するか、DbContext を再作成することを検討してください。

例:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>派生型に対するインクルード

`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。

次のモデルがあるとします。

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。

* キャストを使用する

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* `as` 演算子を使用する

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する

  ```csharp
  context.People.Include("School").ToList()
  ```
