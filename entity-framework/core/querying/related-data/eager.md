---
title: 関連データの一括読み込み - EF Core
description: Entity Framework Core による関連データの一括読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003576"
---
# <a name="eager-loading-of-related-data"></a>関連データの一括読み込み

## <a name="eager-loading"></a>一括読み込み

`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。 次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。

複数のリレーションシップの関連データを 1 つのクエリに含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> コレクション ナビゲーションを 1 つのクエリで一括で読み込むと、パフォーマンスの問題が発生する可能性があります。 詳細については、[単一クエリと分割クエリ](xref:core/querying/single-split-queries)に関するページをご覧ください。

## <a name="including-multiple-levels"></a>複数のレベルを含める

`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。 次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

これらのすべての呼び出しを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。 たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。 両方を含めるには、ルートから始まる各インクルード パスを指定する必要があります。 たとえば、`Blog -> Posts -> Author` や `Blog -> Posts -> Tags`す。 これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は組み合わされます。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> 1 つの `Include` メソッドを使用して複数のナビゲーションを読み込むこともできます。 これは、すべて参照であるナビゲーション "チェーン" の場合、または 1 つのコレクションで終わる場合に可能です。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a>フィルター処理されたインクルード

> [!NOTE]
> この機能は EF Core 5.0 で導入されました。

インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を追加できます。これにより、結果のフィルター処理と並べ替えを行うことができます。

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

> [!NOTE]
> 追跡クエリの場合は、フィルター処理されたインクルードが適用されたナビゲーションが読み込まれると見なされます。 つまり、EF Core では、一部の要素が欠落してる可能性があっても、[明示的読み込み](xref:core/querying/related-data/explicit)または[遅延読み込み](xref:core/querying/related-data/lazy)を使用した値の再読み込みが試みられません。

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
