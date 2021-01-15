---
title: 変更の追跡 - EF Core
description: EF Core の変更の追跡に関する概要
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 52223e5472b09271d19ac9449a3989b4a0e277f7
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129520"
---
# <a name="change-tracking-in-ef-core"></a>EF Core での変更の追跡

各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスによって、エンティティに加えられる変更が追跡されます。 さらに、これらの追跡対象エンティティによって、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> が呼び出されたときにデータベースへの変更が実行されます。

このドキュメントでは、Entity Framework Core (EF Core) の変更の追跡に関する概要と、それがクエリと更新にどのように関連しているかについて説明します。

> [!TIP]
> このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)することで実行およびデバッグできます。

> [!TIP]
> わかりやすくするために、このドキュメントでは <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> などの同期メソッドを使用および参照しています。その非同期バージョンである <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> などは使用していません。 特に明記されていない限り、非同期メソッドの呼び出しと待機は置き換えることができます。

## <a name="how-to-track-entities"></a>エンティティを追跡する方法

エンティティ インスタンスは、次の場合に追跡が開始されます。

- データベースに対して実行されたクエリから返されるとき
- `Add`、`Attach`、`Update`、または同様のメソッドによって明示的に DbContext にアタッチされるとき
- 既存の追跡対象エンティティに接続された新しいエンティティとして検出されるとき

次の場合、エンティティ インスタンスは追跡されなくなります。

- DbContext が破棄されるとき
- 変更トラッカーがクリアされるとき (EF Core 5.0 以降)
- エンティティが明示的にデタッチされるとき

DbContext は、[DbContext の初期化と構成](xref:core/dbcontext-configuration/index)に関する記事で説明されているように、有効期間の短い作業単位を表すように設計されています。 つまり、DbContext の破棄は、エンティティの追跡を停止するための "_通常の方法_" です。 言い換えると、DbContext の有効期間は次のようになります。

1. DbContext インスタンスを作成する
2. いくつかのエンティティを追跡する
3. エンティティにいくつかの変更を加える
4. SaveChanges を呼び出してデータベースを更新する
5. DbContext インスタンスを破棄する

> [!TIP]
> この方法を採用する場合は、変更トラッカーをクリアしたり、エンティティ インスタンスを明示的にデタッチしたりする必要はありません。 ただし、エンティティをデタッチする必要がある場合は、エンティティを 1 つずつデタッチするよりも <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> を呼び出す方が効率的です。

## <a name="entity-states"></a>エンティティの状態

すべてのエンティティは、特定の <xref:Microsoft.EntityFrameworkCore.EntityState> に関連付けられています。

- `Detached` エンティティは <xref:Microsoft.EntityFrameworkCore.DbContext> によって追跡されていません。
- `Added` エンティティは新規で、まだデータベースに挿入されていません。 つまり、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> が呼び出されたときに挿入されます。
- `Unchanged` エンティティは、データベースからクエリされてから変更されて "_いません_"。 クエリから返されるすべてのエンティティは、最初はこの状態になります。
- `Modified` エンティティは、データベースからクエリされてから変更されています。 つまり、SaveChanges が呼び出されたときに更新されます。
- `Deleted` エンティティはデータベースに存在していますが、SaveChanges が呼び出されたときに削除されるようにマークされています。

EF Core では、プロパティ レベルで変更が追跡されます。 たとえば、1 つのプロパティ値のみが変更された場合、データベースの更新によってその値のみが変更されます。 ただし、プロパティは、エンティティ自体が Modified 状態である場合にのみ、変更済みとしてマークされます。 (別の観点から見ると、Modified 状態は、少なくとも 1 つのプロパティ値が変更済みとしてマークされていることを意味します。)

次の表は、さまざまな状態をまとめたものです。

| エンティティ状態     | DbContext によって追跡される | データベースに存在する | プロパティが変更された | SaveChanges でのアクション
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | いいえ                   | -                  | -                   | -
| `Added`          | はい                  | いいえ                 | -                   | 挿入
| `Unchanged`      | はい                  | はい                | いいえ                  | -
| `Modified`       | はい                  | はい                | はい                 | 更新
| `Deleted`        | はい                  | はい                | -                   | 削除

> [!NOTE]
> このテキストでは、わかりやすくするために、リレーショナル データベースの用語を使用しています。 通常、NoSQL データベースでも同様の操作がサポートされていますが、名前が異なる可能性があります。 詳細については、お使いのデータベース プロバイダーのドキュメントを参照してください。

## <a name="tracking-from-queries"></a>クエリからの追跡

EF Core の変更の追跡が最も効果を発揮するのは、同じ <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを使用してエンティティのクエリを実行し、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> を呼び出して更新する場合です。 これは、EF Core によって、クエリされたエンティティの状態が自動的に追跡され、SaveChanges が呼び出されたときにこれらのエンティティに加えられた変更が検出されるためです。

この方法には、[エンティティ インスタンスを明示的に追跡する](xref:core/change-tracking/explicit-tracking)場合と比べていくつかの利点があります。

- 単純である。 エンティティの状態を明示的に操作することが必要になる場合はほとんどありません。EF Core によって状態の変化が対処されます。
- 更新の対象は、実際に変更された値のみに制限されます。
- [シャドウ プロパティ](xref:core/modeling/shadow-properties)の値は保持され、必要に応じて使用されます。 これは、外部キーがシャドウ状態で格納されている場合に特に重要になります。
- プロパティの元の値は自動的に保持され、効率的な更新のために使用されます。

## <a name="simple-query-and-update"></a>シンプルなクエリと更新

たとえば、シンプルなブログ/投稿モデルを考えてみましょう。

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

このモデルを使用して、ブログや投稿に対するクエリを実行した後、データベースにいくつかの更新を行うことができます。

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

SaveChanges を呼び出すと、次のデータベースの更新が発生します (データベースの例として SQLite を使用しています)。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

[変更トラッカーのデバッグ ビュー](xref:core/change-tracking/debug-views)は、追跡されているエンティティとその状態を視覚化するための優れた方法です。 たとえば、上記のサンプルの SaveChanges を呼び出す前に、次のコードを挿入します。

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

次の出力が生成されます。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

特に以下の点に注目してください。

- `Blog.Name` プロパティは変更済み (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) としてマークされており、その結果、ブログの状態が `Modified` になっています。
- 投稿 2 の `Post.Title` プロパティは変更済み (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) としてマークされており、その結果、この投稿の状態が `Modified` になっています。
- 投稿 2 のその他のプロパティ値は変更されていないため、変更済みとしてマークされていません。 このため、これらの値はデータベースの更新に含まれていません。
- その他の投稿は、どのような方法でも変更されていません。 そのため `Unchanged` 状態のままであり、データベースの更新には含まれていません。

## <a name="query-then-insert-update-and-delete"></a>クエリの後に挿入、更新、および削除を行う

前の例に含まれているような更新は、同じ作業単位の中で挿入や削除と組み合わせることができます。 例:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

次の点に注意してください。

- ブログと関連する投稿がデータベースからクエリされ、追跡されます
- `Blog.Name` プロパティが変更されます
- ブログの既存の投稿のコレクションに新しい投稿が追加されます
- 既存の投稿が、<xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> を呼び出すことによって削除対象としてマークされます

SaveChanges を呼び出す前に[変更トラッカーのデバッグ ビュー](xref:core/change-tracking/debug-views)をもう一度確認すると、EF Core によってこれらの変更がどのように追跡されているかがわかります。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

次のことに注意してください。

- ブログは `Modified` としてマークされています。 これにより、データベースの更新が生成されます。
- 投稿 2 は `Deleted` としてマークされています。 これにより、データベースの削除が生成されます。
- 一時的な ID を持つ新しい投稿がブログ 1 に関連付けられ、`Added` としてマークされています。 これにより、データベースの挿入が生成されます。

これによって、SaveChanges が呼び出されたときに、次のデータベース コマンド (SQLite を使用) が生成されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

エンティティの挿入と削除の詳細については、「[エンティティの明示的な追跡](xref:core/change-tracking/explicit-tracking)」を参照してください。 EF Core でこのような変更が自動的に検出されるしくみについて詳しくは、「[変更の検出と通知](xref:core/change-tracking/change-detection)」を参照してください。

> [!TIP]
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> を呼び出すと、SaveChanges によってデータベースが更新されるような変更が行われたかどうかを確認できます。 HasChanges から false が返される場合は、SaveChanges によって何も行われません。
